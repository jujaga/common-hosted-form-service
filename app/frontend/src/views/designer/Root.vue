<template>
  <form>
    <formly-form :form="form" :model="model" :fields="fields" />
    <v-btn color="primary" @click="submit">
      <span>Submit</span>
    </v-btn>
  </form>
</template>

<script>
export default {
  name: 'Editor',
  data: () => ({
    form: {},
    model: {
      fname: '',
      lname: '',
    },
    fields: [
      {
        key: 'fname',
        type: 'input',
        wrapper: '<div>First Name</div><div class="col-12"></div>',
      },
      {
        key: 'lname',
        type: 'input',
        wrapper: '<div>Last Name</div><div class="col-12"></div>',
      }
    ],
  }),
  methods: {
    submit() {
      console.log(JSON.stringify(this.model)); // eslint-disable-line no-console
    }
  },
  props: {
    formId: String,
    formVersionId: String,
  },
};
</script>
