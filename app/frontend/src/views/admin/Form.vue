<template>
  <v-container>
    <h1 class="mt-6">Administer Form</h1>
    <AdministerForm :formId="f" />
  </v-container>
</template>

<script>
import AdministerForm from '@/components/admin/AdministerForm.vue';

export default {
  name: 'AdminFormView',
  components: {
    AdministerForm,
  },
  props: {
    f: String,
  },
};
</script>
