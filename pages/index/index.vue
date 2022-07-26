<template>
  <div id="PageForm">
    <div class="mydata_container">
      <b-field class="field_name" label="Your Email">
        <b-autocomplete 
        placeholder="Your Email" 
        v-model="yourname"
        :data="filteredName"
        @select="selectedYouremail"
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
    <b-table :data="scored_data" :columns="column" class="table"></b-table>

    <div class="competent_column" v-show="selected_yourname != null">
      <div v-if="competent_form_validity == false"> 
        <b-field class="field_name" label="Most Competent Employee ">
        <b-autocomplete 
        placeholder="Email" 
        v-model="competent"
        :data="filteredcompetent"
        @select="competent_selected"
        :disabled="selected_competent != null"
        :maxlength="50">
        <template #empty>Your name is not registered yet / not found</template>
        </b-autocomplete>
      </b-field>
      <b-field class="field_name" label="Most Uncompetent Employee ">
        <b-autocomplete 
        placeholder="Email" 
        v-model="uncompetent"
        :data="filtereduncompetent"
        @select="uncompetent_selected"
        :disabled="selected_uncompetent != null"
        :maxlength="50">
        <template #empty>Your name is not registered yet / not found</template>
        </b-autocomplete>
      </b-field>
      <b-button type="is-primary" :disabled="this.competent_form == false"
        @click="submit_competent()">Submit</b-button>
      </div>
    </div>

    <fieldset class="reviewform_container">
      <legend>Review Form</legend>
      <div class="targetname_container">
        <b-field label="Name" class="field_name" >
          <b-autocomplete value="" placeholder="Name"
          :disabled="yourname_validity == false || targetname_validity == true"
          @select="option => selected_targetname = option"
          :data="filteredTargetName"
          v-model="targetname">
          </b-autocomplete>
        </b-field>
        <font-awesome-icon 
          v-show="targetname_validity == true"
          @click="targetname_cancel"
          class="delete_icon"
          icon="fa-solid fa-xmark">
        </font-awesome-icon>
      </div>
      <section :disabled="targetname_validity == false">
      </section>
      <label><font-awesome-icon icon="fa-solid fa-user" /> Quality</label>
      <b-field>
        <b-slider 
        :min="1" :max="5"
        v-model="quality"
        :disabled="targetname_validity == false"
        :custom-formatter="val => 'Quality : ' + val"
        ticks rounded></b-slider>
      </b-field>
      <label><font-awesome-icon icon="fa-solid fa-person-running" /> Speed</label>
      <b-field>
        <b-slider 
        :min="1" :max="5"
        v-model="speed"
        :disabled="targetname_validity == false"
        :custom-formatter="val => 'Speed : ' + val"
        ticks rounded></b-slider>
      </b-field>
      <label><font-awesome-icon icon="fa-solid fa-heart" /> Relation</label>
      <b-field>
        <b-slider 
        :min="1" :max="5" 
        v-model="relation"
        :disabled="targetname_validity == false"
        :custom-formatter="val => 'Relation : ' + val"
        ticks rounded></b-slider>
      </b-field>
      <b-field label="Kelebihan"
        :label-position="'on-border'">
        <b-input maxlength="500" v-model="kelebihan" :disabled="targetname_validity == false" type="textarea"></b-input>
      </b-field>
      <b-field label="Kekurangan"
        :label-position="'on-border'">
        <b-input maxlength="500" v-model="kekurangan" :disabled="targetname_validity == false" type="textarea"></b-input>
      </b-field>
      <b-field label="More Info"
        :label-position="'on-border'">
        <b-input maxlength="500" v-model="moreinfo" :disabled="targetname_validity == false" type="textarea"></b-input>
      </b-field>
      <b-button type="is-primary" :disabled="this.form_valid == false"
        @click="submit_scoring">Submit</b-button>
    </fieldset>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PageForm',
  data() {
    return {
        reviewdata: [],
        competent_form_validity: true,
        api_url: 'http://127.0.0.1:8000/',
        yourname: '',
        selected_yourname: null,
        targetname: '',
        selected_targetname: null,
        quality: 1,
        speed: 1,
        relation: 1,
        kelebihan: '',
        kekurangan: '',
        radio: 'com',
        moreinfo: '',
        NameData: [],
        karyawandata: [],
        competent: '',
        uncompetent: '',
        selected_competent: null,
        selected_uncompetent: null,
        column: [
          {
            field: 'email',
            label: 'Email',
            width: '200px'
          },
          {
            field: 'status',
            label: 'Status',
            width: '200px',
          },
        ],
        scored_data: [],
    }
  },
  computed: {
    competent_form(){
      if(this.competent == '' || this.uncompetent == ''){
        return false
      }else{
        return true
      }
    },
    form_valid(){
      if(this.yourname != '' && this.targetname != '' && this.quality != '' && this.speed != '' && this.relation != '' && this.kelebihan != '' && this.kekurangan != '' && this.moreinfo != ''){
        return true;
      }
      else{
        return false;
      }
    },
    filteredName() {
      if (this.NameData.length === 0) {
        return [];
      }
      return this.NameData.map(item => item.email).filter(item => item.toLowerCase().indexOf(this.yourname.toLowerCase()) > -1);
    },
    filteredTargetName(){
      if (this.NameData.length === 0) {
        return [];
      }
      return this.NameData.map(item => item.email).filter(item => item.toLowerCase().indexOf(this.targetname.toLowerCase()) > -1 && item != this.yourname);
    },
    filteredcompetent(){
      if (this.NameData.length === 0) {
        return [];
      }
      return this.NameData.map(item => item.email).filter(item => item.toLowerCase().indexOf(this.targetname.toLowerCase()) > -1 && item != this.selected_uncompetent);
    },
    filtereduncompetent(){
      if (this.NameData.length === 0) {
        return [];
      }
      return this.NameData.map(item => item.email).filter(item => item.toLowerCase().indexOf(this.targetname.toLowerCase()) > -1 && item != this.selected_competent);
    },
    yourname_validity() {
      return this.selected_yourname != null
    },
    targetname_validity() {
      return this.selected_targetname != null;
    },
  },
  methods: {
    competent_selected(emails){
      this.competent = emails;
      this.selected_competent = emails;
    },
    uncompetent_selected(emails){
      this.uncompetent = emails;
      this.selected_uncompetent = emails;
    },
    async submit_scoring(){
      let yourname_id = this.karyawandata.find(item => item.email == this.selected_yourname).id;
      let targetname_id = this.karyawandata.find(item => item.email == this.selected_targetname).id;
      let data = {
        id_from: yourname_id,
        id_to: targetname_id,
        quality: this.quality,
        speed: this.speed,
        relation: this.relation,
        kelebihan: this.kelebihan,
        kurang: this.kekurangan,
        additional_info: this.moreinfo,
      }
      let pass = 0;
      await axios.get(this.api_url + 'check/'+yourname_id+'/'+targetname_id)
      .then(response => {
        console.log(response.data);
        if(response.data.status == 'failed'){
              alert('You already scored this person');
              pass = 1;
        }
      });
      if(pass == 0){
        await axios.post(this.api_url + 'scoring/', data)
        .then(response => {
          alert('Success');
        })
        .catch(error => {
          console.log(error);
        })
      }
      this.init_scored_karyawan(yourname_id);
      this.targetname_cancel();
    },
    async submit_competent(target1,target2){
      let yourname_id = this.karyawandata.find(item => item.email == this.selected_yourname).id;
      let competent_id = this.karyawandata.find(item => item.email == this.selected_competent).id;
      let uncompetent_id = this.karyawandata.find(item => item.email == this.selected_uncompetent).id;
      let value = true;
      await axios.put(this.api_url + 'karyawan/'+competent_id, {
        value: true,
      })
        .then(response => {
          console.log(response.data);
      });
      await axios.put(this.api_url + 'karyawan/'+uncompetent_id, {
        value: false,
        })
        .then(response => {
          console.log(response.data);
      });
      await axios.put(this.api_url + 'scoringcomp/'+yourname_id)
        .then(response => {
          console.log(response.data);
      });
      this.yourname_cancel();
    },
    selectedYouremail(email_items){
      this.selected_yourname = email_items;
      if(email_items == null){
          return false;
      }
      this.yourname = email_items;
      let selectedkaryawan = this.karyawandata.filter(item => item.email == this.selected_yourname);
      let yourname_id = selectedkaryawan[0].id;
      this.init_scored_karyawan(yourname_id);
      this.check_form_validity(email_items);
    },
    check_form_validity(name){  
      let selectedkaryawan = this.karyawandata.filter(item => item.email == name);
      console.log(selectedkaryawan);
      if(selectedkaryawan[0].choose_competence == 1 && selectedkaryawan[0].choose_uncompetence == 1){
        this.competent_form_validity = true;
      }else{
        this.competent_form_validity = false;
      }
    },
    yourname_cancel() {
      this.yourname = '';
      this.selected_yourname = null;
      this.scored_data = [];
      this.reviewdata = [];
      this.targetname_cancel();
    },
    targetname_cancel() {
      this.targetname = '';
      this.selected_targetname = null;
      this.kelebihan = '';
      this.kekurangan = '';
      this.moreinfo = '';
      this.speed = 1;
      this.quality = 1;
      this.relation = 1;
    },
    async init_scored_karyawan(id){
      await axios.get(this.api_url + 'scoringall')
        .then(response => {
          this.reviewdata = response.data;
        })
        .catch(error => {
          console.log(error);
      });
      this.scored_data = this.karyawandata.map(item => {
        let status = 'not scored';
        if(this.reviewdata.find(item2 => item2.id_to == item.id && item2.id_from == id)){
          status = 'scored';
        }
        if(item.id == id){
          status = 'its you';
        }
        return {
          email: item.email,
          status: status,
        }
      });
    },
    initdata(){
      axios.get(this.api_url + 'karyawan')
        .then(response => {
          this.NameData = response.data.map(item => {
            return {
              email: item.email
            }
          });
          this.karyawandata = response.data;
        })
        .catch(error => {
          console.log(error);
        })
      },
  },
  async created() {
    await this.initdata();
  }
}
</script>

<style src="./index.css" scoped>