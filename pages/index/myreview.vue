<template>
  <div id="myreview">
    <div class="mydata_container">
      <b-field class="field_name" label="Your Name">
        <b-autocomplete 
        placeholder="Your Name" 
        v-model="yourname"
        :data="filteredName"
        @select="getSelected"
        :disabled="yourname_validity == true"
        :maxlength="50">
        <template #empty>Your name is not registered yet / not found</template>
        </b-autocomplete>
      </b-field>
      <font-awesome-icon 
        v-show="yourname_validity == true"
        @click="yourname_cancel"
        class="delete_icon"
        icon="fa-solid fa-xmark">
      </font-awesome-icon>
    </div>
    <div class="mydatadesc_container">
      <h3>Reviews Total : {{ reviewstotal }}</h3>
      <h2>Competent Count : {{ com_count }}</h2>
      <h2>Non-Competent Count : {{ non_count }}</h2>
      <div class="mydatadesc_tablescore">
        <b-table :data="data" :columns="columns"></b-table>
      </div>
      <div class="mydatadesc_info">
        <div v-for="item in allreviews" :key="item.id">
          <span>-----------</span>
          <p>Kelebihan : {{ item.kelebihan }}</p>
          <p>Kekurangan : {{ item.kurang }}</p>
          <p>More Info : {{ item.additional_info }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "MyReview",
  data(){
    return{
      yourname: "",
      reviewstotal: 0,
      selected_yourname: null,
      api_url: 'http://127.0.0.1:8000/',
      NameData: [],
      karyawandata: [],
      allreviews: [],
      com_count: 0,
      non_count: 0,
      averages: [
        {
          name: "Quality",
          value: 0
        },
        {
          name: "Speed",
          value: 0
        },
        {
          name: "Relation",
          value: 0
        }
      ],
      columns: [
        {
          label: 'Type',
          field: 'type',
          width: '50%'
        },
        {
          label: 'Average Score',
          field: 'score',
          width: '50%'
        }
      ],
    }
  },
  computed: {
    data(){
      return this.averages.map(item => {
        return {
          'type': item.name,
          'score': item.value.toFixed(2)
        }
      })
    },
    filteredName(){
      if (this.NameData.length === 0) {
        return [];
      }
      return this.NameData.map(item => item.full_name).filter(item => item.toLowerCase().indexOf(this.yourname.toLowerCase()) > -1);
    },
    yourname_validity(){
      return this.selected_yourname != null
    },
  },
  methods:{
      async getSelected(item){
        this.selected_yourname = item;
        if(item == null){
          return false;
        }
        let name = item;
        let selected_id = this.karyawandata.find(item => item.full_name == name).id;
        await this.getreviews(selected_id);
        await this.getkaryawancompetence(selected_id);
        await this.getaverages();
      },
      yourname_cancel(){
        this.yourname = "";
        this.selected_yourname = null;
        this.allreviews = [];
        this.reviewstotal = 0;
        this.averages = [
          {
            name: "Quality",
            value: 0
          },
          {
            name: "Speed",
            value: 0
          },
          {
            name: "Relation",
            value: 0
          }
        ];
      },
      async getkaryawancompetence(id){
        this.com_count = this.karyawandata.find(item => item.id == id).competence_count;
        this.non_count = this.karyawandata.find(item => item.id == id).uncompetence_count;
      },
      async getreviews(id){
        await axios.get(this.api_url + 'scoring/' + id)
        .then(response => {
          this.allreviews = response.data;
          this.reviewstotal = this.allreviews.length;
        })
        .catch(error => {
          console.log(error);
        })
      },
      getaverages(){
        let quality = 0;
        let speed = 0;
        let relation = 0;
        this.allreviews.forEach(item => {
          quality += item.quality;
          speed += item.speed;
          relation += item.relation;
        });
        this.averages[0].value = quality / this.allreviews.length;
        this.averages[1].value = speed / this.allreviews.length;
        this.averages[2].value = relation / this.allreviews.length;
      },
      initdata(){
        axios.get(this.api_url + 'karyawan')
        .then(response => {
          this.NameData = response.data.map(item => {
            return {
              full_name: item.full_name
            }
          });
          this.karyawandata = response.data;
        })
        .catch(error => {
          console.log(error);
        })
      },
  },
  created(){
    this.initdata();
  }
}
</script>

<style src="./myreview.css" />