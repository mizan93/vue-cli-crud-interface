<template>
   <div id="main-app" class="container">
       <div class="row justify-content-center">
              
         <add-appointment @add='addItem'></add-appointment>
         <search-appintment @searchRecords='SearchAppointments' :myKey="filterKey"
         :myDir="filterDir"
         @requestKey="changeKey"
         @requestDir="changeDir"
         ></search-appintment>
         <appointment-list :appointments="filterApts" @remove="removeItem" @edit="editItem" ></appointment-list>
      </div>
   </div>
</template>

<script>
import SearchAppintment from './components/SearchAppointments.vue'
import AddAppointment from './components/AddAppointment.vue'
import AppointmentList from './components/AppointmentList.vue'
import axios from 'axios'
import _ from 'lodash'
export default {
  name: 'MainApp',
  data() {
    return {
    filterKey:'petName',
    filterDir:'asc',
      appointments:[],
      searchTerms:'',
      aptIndex:0
    }
  },
  components: {
   SearchAppintment,
    AddAppointment,
     AppointmentList,
  },
  mounted(){
  axios.get('./data/appointments.json')
  .then(resposne=>(this.appointments=resposne.data.map(item=>{
    item.aptId=this.aptIndex
    this.aptIndex++
    return item
  })))
  },
  computed: {
    searchedapts(){
      return  this.appointments.filter(item=>{
        return(
          item.petName.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.petOwner.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.aptNotes.toLowerCase().match(this.searchTerms.toLowerCase())
        )
      })
    },
    filterApts(){
      return _.orderBy(
        this.searchedapts,
        item=>{
          return item[this.filterKey].toLowerCase()
        },this.filterDir
      )
    }
  },
  methods: {
    changeKey(value){
this.filterKey=value
    },
    changeDir(value){
this.filterDir=value
    },
    SearchAppointments(terms){
      this.searchTerms=terms
        
    },
     addItem(apt){
        apt.aptId=this.aptIndex
        this.aptIndex++
        this.appointments.push(apt)
    },
    removeItem(apt){
      this.appointments= _.without(this.appointments,apt);
    },
    editItem(id,field,text){
      const aptIndex=_.findIndex(this.appointments,{
        aptId:id
      })
      this.appointments[aptIndex][field]=text
    },
   
  },
}
</script>


