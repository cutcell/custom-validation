<template>
  <div class="container-fluid">
    
    <div v-if="hasErrors" class="bg-danger text-white my-2 p-2">
      <h5>Errors:</h5>
      <ul>
        <template v-for="errors in validationErrors">
          <li v-for="error in errors" :key="error">{{ error }}</li>
        </template>
      </ul>
    </div>

    <div class="btn-primary text-white p-2">
      Name: {{ name }}, Category: {{ category }}, Price: {{ price }}
    </div>

    <form @submit.prevent="handleSubmit">
      <div class="form-group">
        <label>Name</label>
        <input type="text" v-model="name" class="form-control">
      </div>
      <div class="form-group">
        <label>Category</label>
        <input type="text" v-model="category" class="form-control">
      </div>
      <div class="form-group">
        <label>Price</label>
        <input type="number" v-model="price" class="form-control">
      </div>
      <div class="text-center">
        <button class="btn btn-success" type="submit">Submit</button>
      </div>
    </form>

  </div>
</template>

<script>

import validator from './validationRules';
import Vue from 'vue';

export default {
  name: 'app',
  data() {
    return {
      name: '',
      category: '',
      price: 0,
      validationErrors: {},
      submittedOnce: false
    }
  },
  computed: {
    hasErrors() {
      return Object.values(this.validationErrors).length > 0;
    }
  },
  watch: {
    name(value) { this.validateWatch(value, "name"); },
    category(value) { this.validateWatch(value, "category"); },
    price(value) { this.validateWatch(value, "price"); },
  },
  methods: {
    validateWatch(propertyName, value) {
      if (this.submittedOnce) {
        this.validate(propertyName, value);
      }
    },
    validate(propertyName, value) {
      let errors = [];
      Object(validator)[propertyName].forEach(v => {
        if (!v.validator(value)) {
          errors.push(v.message);
        }
      });
      if (errors.length > 0) {
        Vue.set(this.validationErrors, propertyName, errors);
      } else {
        Vue.delete(this.validationErrors, propertyName);
      }
    },
    validateAll() {
      this.validate("name", this.name);
      this.validate("category", this.category);
      this.validate("price", this.price);
      return this.hasErrors;
    },
    handleSubmit() {
      this.submittedOnce = true;
      if (this.validateAll()) {
        console.log(`Form submitted: ${this.name} ${this.category} ${this.price}`);
      }
    }
  }
}
</script>

