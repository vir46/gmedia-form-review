<template>
  <div id="PageForm">
    <div class="mydata_container">
      <b-field class="field_name" label="Your Name">
        <b-autocomplete 
        placeholder="Your Name" 
        v-model="yourname"
        :data="filteredName"
        @select="option => selected_yourname = option"
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
        <b-field>
            <b-radio v-model="radio"
                native-value="com"
                size="is-small">
                Kompeten
            </b-radio>
        </b-field>
        <b-field>
          <b-radio v-model="radio"
              size="is-small"
              native-value="non">
              Tidak Kompeten
          </b-radio>
        </b-field>
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
    }
  },
  computed: {
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
      return this.NameData.map(item => item.full_name).filter(item => item.toLowerCase().indexOf(this.yourname.toLowerCase()) > -1);
    },
    filteredTargetName(){
      if (this.NameData.length === 0) {
        return [];
      }
      return this.NameData.map(item => item.full_name).filter(item => item.toLowerCase().indexOf(this.targetname.toLowerCase()) > -1 && item != this.yourname);
    },
    yourname_validity() {
      return this.selected_yourname != null
    },
    targetname_validity() {
      return this.selected_targetname != null;
    },
    async targetCheck(name) {
      this.selected_targetname = name;
      let yourname_id = this.karyawandata.find(item => item.full_name == name).id;
      let targetname_id = this.karyawandata.find(item => item.full_name == this.selected_yourname).id;
      console.log(yourname_id, targetname_id);
    },
  },
  methods: {
    async submit_scoring(){
      let competent = {
        radio: this.radio,
      }
      let yourname_id = this.karyawandata.find(item => item.full_name == this.selected_yourname).id;
      let targetname_id = this.karyawandata.find(item => item.full_name == this.selected_targetname).id;
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
        await axios.put(this.api_url + 'karyawan/'+targetname_id, competent)
        .then(response => {
          console.log(response.data);
        });
        await axios.post(this.api_url + 'scoring/', data)
        .then(response => {
          alert('Success');
        })
        .catch(error => {
          console.log(error);
        })
      }
      this.yourname_cancel();
    },
    yourname_cancel() {
      this.yourname = '';
      this.selected_yourname = null;
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
  async created() {
    await this.initdata();
  }
}
</script>

<style src="./index.css" scoped>