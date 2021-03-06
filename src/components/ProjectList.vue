<template>
<section>
    <v-layout>
        <v-card>
            <v-card-title>
                La plateforme Simplon Open Source permet aux formateurs comme aux apprenants de poster des projets, aboutis ou en cours, individuels en ou en groupe, et de les partager avec l'ensemble de la communauté.
            </v-card-title>
        </v-card>
    </v-layout>
      <v-switch
        :label="`Production projets only (no WIP)`"
        v-model="wip"
        @change="filter_by_name"
      ></v-switch>
      <v-text-field
        hide-details
        single-line
        v-show="searchStatus"
        label="Javascript, Php"
        autofocus
        v-model="search_input"
        @keyup="filter_by_name"
      ></v-text-field>
      <v-btn icon @click="toggleSearchBar">
        <v-icon :class="searchStatus ? 'primary--text' : 'dark--text lighten-1'">fas fa-search</v-icon>
      </v-btn>
      <section :key="tag" v-for="tag in selected_tags">
              <v-chip @click="remove_filter_tag(tag)"><v-icon>fas fa-times</v-icon>  {{ tag }}</v-chip>
            </section>
	<v-container grid-list-md>
		<v-layout row wrap >
			<v-flex :key="project.id" v-for="project in this.projects_list.projects">
        <v-card color="grey lighten-3">
          <v-card-title>
            <v-layout row>
              <v-flex xs1>
              <v-icon x-large>{{project.icon}}</v-icon>
              </v-flex>
              <v-flex xs11 class="text-xs-right">
                <h3>{{ project.name }}</h3>
                <p>{{ project.description }}</p>
              </v-flex>
              <v-btn flat @click="linkToProject(project.id)">Show more</v-btn>
            </v-layout>
          </v-card-title>
          <v-divider darken></v-divider>
          <v-card-actions>
            <section :key="tag" v-for="tag in project.tags">
              <v-chip @click="add_filter_tag(tag)">{{ tag }}</v-chip>
            </section>
            <v-spacer>
              </v-spacer>
              <v-chip v-if=project.wip color="amber lighten-2">WIP</v-chip>
              <v-chip v-if=!project.wip color="light-green accent-4">OK</v-chip>
            <v-spacer>
              </v-spacer>
            <v-btn-toggle>
              <v-btn :href="project.demo">
                <v-icon>fas fa-globe</v-icon>
              </v-btn>
              <v-btn :href="`${project.link}`">
                <v-icon>
                  fab fa-{{ project.link.startsWith('https://github')?'github':'gitlab' }}
                </v-icon>
              </v-btn>
            </v-btn-toggle>
          </v-card-actions>
        </v-card>
        <hr/>
			</v-flex>
		</v-layout>
	</v-container>
</section>
</template>

<script>
import router from "../router";

export default {
  props: {
    projects_list: Object,
  },
  data() {
    return {
      init_projects: this.projects_list.projects,
      projects: this.projects_list.projects,
      github_data: null,
      selected_tags: []
    };
  },
  methods: {
    linkToProject(id) {
      router.push({ name: "project", params: { project_id: id } });
    },
    add_filter_tag(tag){
      if (this.selected_tags.indexOf(tag) == -1){
      this.selected_tags.push(tag);
      this.filter_by_tags();
      }
    },
    remove_filter_tag(tag){
      this.selected_tags.splice(this.selected_tags.indexOf(tag), 1);
      this.filter_by_tags();
    },
    filter_by_tags(){
      this.projects = this.init_projects.filter(project => {
        return this.selected_tags.every(
          selected_tag => project.tags.indexOf(selected_tag) != -1
        ) 
      });
    },
  }
};
</script>

<style scoped>
p {
  width: 80%;
  float: right;
}
</style>
