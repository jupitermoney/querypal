<template>
  <b-row style="margin-top: 10px">
    <DatabaseSearch/>
    <SQLEditorTabs/>
    <v-tour name="myTour" :steps="steps"></v-tour>
  </b-row>
</template>

<script>
import SQLEditorTabs from '@/components/SQLEditorTabs'
import DatabaseSearch from '@/components/DatabaseSearch'
export default {
  name: "QueryPalApp",
  components: {SQLEditorTabs, DatabaseSearch},
  mounted() {
    this.$tours['myTour'].start()
  },
  data() {
    return {
      steps: [
        {
          target: '#v-step-0',  // We're using document.querySelector() under the hood
          header: {
            title: 'Explore Amazon Athena Databases',
          },
          content: `Explore <strong>all Amazon Athena Databases</strong> in your data lake!`,
          params: {
            placement: 'right'
          }
        },
        {
          target: '#v-step-1',  // We're using document.querySelector() under the hood
          header: {
            title: 'Explore Athena Tables',
          },
          content: `Explore <strong>tables in the selected Athena Databases</strong>!`,
          params: {
            placement: 'bottom'
          }
        },
        {
          target: '#v-step-2',
          content: `Write SQL Queries to explore tables. E.g <strong>SELECT CURRENT_DATE as my_date;</strong>`,
          header: {
            title: 'SQL Editor',
          },
          params: {
            placement: 'bottom'
          }
        }
      ]
    }
  },
  methods: {

  }
}
</script>

<style scoped>

</style>
