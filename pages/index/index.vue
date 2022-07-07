<template>
  <div id="PageForm">
    <div class="mydata_container">
      <b-field label="Your Name">
        <b-input value="" placeholder="Your Username"></b-input>
      </b-field>
    </div>
    <fieldset class="reviewform_container">
      <legend>Review Form</legend>
      <b-field label="Name">
        <b-input value="" placeholder="Name"></b-input>
      </b-field>
      <label><font-awesome-icon icon="fa-solid fa-user" /> Quality</label>
      <b-field>
        <b-slider 
        :min="1" :max="5" 
        :custom-formatter="val => 'Quality : ' + val"
        ticks rounded></b-slider>
      </b-field>
      <label><font-awesome-icon icon="fa-solid fa-person-running" /> Speed</label>
      <b-field>
        <b-slider 
        :min="1" :max="5" 
        :custom-formatter="val => 'Speed : ' + val"
        ticks rounded></b-slider>
      </b-field>
      <label><font-awesome-icon icon="fa-solid fa-heart" /> Relation</label>
      <b-field>
        <b-slider 
        :min="1" :max="5" 
        :custom-formatter="val => 'Relation : ' + val"
        ticks rounded></b-slider>
      </b-field>
      <b-field label="Kelebihan"
        :label-position="'on-border'">
        <b-input maxlength="200" type="textarea"></b-input>
      </b-field>
      <b-field label="Kekurangan"
        :label-position="'on-border'">
        <b-input maxlength="200" type="textarea"></b-input>
      </b-field>
      <b-field label="More Info"
        :label-position="'on-border'">
        <b-input maxlength="200" type="textarea"></b-input>
      </b-field>
      <b-button type="is-primary">Primary</b-button>
    </fieldset>
  </div>
</template>

<script>
export default {
  name: 'PageForm'
}
</script>

<style src="./index.css" scoped>