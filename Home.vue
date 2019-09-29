<template>
  <b-container>
    <b-row>
      <b-col>
        <h1>Province of British Columbia</h1>
      </b-col>
    </b-row>
    <b-row>
      <b-col id="selectLocationContainer" cols="3">
        <label for="selectLocation">Select aquifer location:</label>
        <b-form-select id="selectLocation" v-model="selectedLocation" :options="aquiferLocations"></b-form-select>
      </b-col>
      <b-col id="selectedLocationContainer">
        Selected location:<br/>
        <b-alert show><strong>{{ selectedLocation }}</strong></b-alert>
        <span v-html="details"></span>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import AQUIFERS from '@/data/aquifers.json'
const SELECTPLACEHOLDER = 'Please select a location'

export default {
  components: {
  },
  data() {
    return {
      selectedLocation: SELECTPLACEHOLDER,
      aquifers: AQUIFERS.results, // TODO: import aquifers from an API
      details: '',
      fields: {'aquifer_id':'Aquifer Id',
               'mapping_year':'Mapping Year',
               'name':'Name',
               'area':'Area',
               'vulnerability':'Vulnerability'}
    }
  },
  computed: {
      aquiferLocations () {
        let locations = this.aquifers
          // Remove null or whitespace locations (must do before sort)
          .filter(item => item.location)
          // Get only locations
          .map(function(item) { 
            let x = item.location.trim()
            return { value: x, text: x }
          })
          // Sort alphabetical by location
          .sort(function(a, b) {
            if (a.value < b.value) { return -1 }
            if (a.value > b.value) { return 1 }
             return 0
          })
          // Return unique values (must do after sort, refactor if performance issue)
          .filter(function(el, idx, arr) {
            return !idx || el.value != arr[idx - 1].value
          })
          // Insert item
          locations.unshift({value: SELECTPLACEHOLDER, text: SELECTPLACEHOLDER, disabled: true })
        return locations
      }
  },
  watch: {
      selectedLocation:function(val){
        // Get all records for selected location
        let locationData = this.aquifers
          .filter(item => ((item!=null)))  // Filter out null items
          .filter(item => ((item.location!=null)))  // Filter out items with null location
          .filter(item => item.location.trim()==val);  // Grab selected location records
        // Clear variable for template field variable
        this.details='';
        // Output table html string initialization
        let locationDetails = '';
        locationDetails=locationDetails+'<table border=1>';
        locationDetails=locationDetails+'<tr>';
        // Build table header
        for (var p in this.fields){
          locationDetails=locationDetails+'<th>'+this.fields[p]+'</th>';
        }
        locationDetails=locationDetails+'</tr>';
        // Loop through all selected records
        for (var i=0;i<locationData.length;i++){
          locationDetails=locationDetails+'<tr>';
          // Output required fields
          for (p in this.fields){
            locationDetails=locationDetails+'<td>'+(locationData[i][p]!=null ? locationData[i][p]:'')+'</td>';
          }
          locationDetails=locationDetails+'</tr>';
        }
        locationDetails=locationDetails+'</table>';
        // Assign table html string to template field variable
        this.details=locationDetails;
      }
  }
};
</script>

<style scoped>
</style>