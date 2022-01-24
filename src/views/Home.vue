<template>
  <div class="home">
    <!-- $event is the var which stores the value passed on $emit. -->
    <FilterNav @filterChange="handleFilterChange" :currentFilter="currentFilter" />
    <div v-if="filteredProjects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject :project="project" 
          @delete-project="handleDelete" 
          @complete-project="handleComplete" />
      </div>
    </div>
  </div>
</template>

<script>
import SingleProject from '../components/SingleProject.vue'
import FilterNav from '../components/FilterNav.vue'

export default {
  name: 'Home',
  components: { SingleProject, FilterNav },
  data() {
    return {
      projects: [],
      currentFilter: 'all'
    }
  },
  mounted() {
    fetch('http://localhost:3000/projects')
      .then((res) => res.json())
      .then((data) => this.projects = data)
      .catch((error) => console.error(error.message))
  },
  methods: {
    handleDelete(id) {
      this.projects = this.projects.filter((project) => project.id !== id)
    },
    handleComplete(id) {
      let project = this.projects.find((project) => project.id === id)
      project.complete = !project.complete
    },
    handleFilterChange(filter) {
      this.currentFilter = filter
    }
  },
  computed: {
    filteredProjects() {
      if (this.currentFilter === 'completed') {
        return this.projects.filter((project) => project.complete)
      } 
      if (this.currentFilter === 'ongoing') {
        return this.projects.filter((project) => !project.complete)
      }
      
      return this.projects
    }
  }
}
</script>
