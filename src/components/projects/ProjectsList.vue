<template>
  <base-container v-if="user">
    <h2>{{ user.fullName }}: Projects</h2>
    <base-search v-if="hasProjects" @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <ul v-if="hasProjects">
      <project-item v-for="prj in availableProjects" :key="prj.id" :title="prj.title"></project-item>
    </ul>
    <h3 v-else>No projects found.</h3>
  </base-container>
  <base-container v-else>
    <h3>No user selected.</h3>
  </base-container>
</template>

<script>
import ProjectItem from './ProjectItem.vue';
import { ref, computed, watch } from 'vue';

export default {
  components: {
    ProjectItem,
  },
  props: ['user'],
  setup(props){
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');
    
    watch(updateSearch, function(newValue){
      setTimeout(() => {
        if (newValue === enteredSearchTerm.value) {
          activeSearchTerm.value = newValue;
        }
      }, 300);
    })

    watch(user, function(){
      enteredSearchTerm.value = '';
    })

    const updateSearch = function(val){
      enteredSearchTerm.value = val;
    }

    const hasProjects = computed(function(){
      return user.projects.value && availableProjects.value.length > 0;
    })
    
    const availableProjects = computed(function(){
      if (activeSearchTerm.value) {
        return user.projects.value.filter((prj) =>
          prj.title.value.includes(activeSearchTerm.value)
        );
      }
      return user.projects.value;
    })

    return {
      enteredSearchTerm,
      activeSearchTerm,
      updateSearch,
      hasProjects,
      availableProjects,
    };
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>