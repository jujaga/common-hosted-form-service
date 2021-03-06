<template>
  <v-container class="px-0">
    <v-row>
      <v-col cols="12" lg="4">
        <v-text-field
          dense
          flat
          solid
          outlined
          label="Form Title"
          data-test="text-name"
          v-model="name"
          :rules="nameRules"
        />
      </v-col>
      <v-col cols="12" lg="8">
        <v-text-field
          dense
          flat
          solid
          outlined
          label="Form Description"
          data-test="text-description"
          v-model="description"
          :rules="descriptionRules"
        />
      </v-col>
    </v-row>

    <p>Select which type of users can launch this form when published</p>
    <v-radio-group
      class="pl-5"
      v-model="userType"
      :mandatory="false"
      :rules="loginRequiredRules"
      @change="userTypeChanged"
    >
      <v-radio label="Public (anonymous)" :value="ID_MODE.PUBLIC" />
      <v-radio label="Log-in Required" value="login"></v-radio>
      <v-row v-if="userType === ID_MODE.LOGIN" class="pl-6 mb-2">
        <v-checkbox
          v-model="idps"
          class="mx-4"
          label="IDIR"
          :value="ID_PROVIDERS.IDIR"
        />
        <v-checkbox
          disabled
          v-model="idps"
          class="mx-4"
          label="BCeID"
          :value="ID_PROVIDERS.BCEID"
        />
        <v-checkbox
          disabled
          v-model="idps"
          class="mx-4"
          label="BC Services Card"
          :value="ID_PROVIDERS.BCSC"
        />
        <v-checkbox
          disabled
          v-model="idps"
          class="mx-4"
          label="Github"
          :value="ID_PROVIDERS.GITHUB"
        />
      </v-row>
      <v-radio label="Specific Team Members" value="team" />
      <v-row v-if="userType === ID_MODE.TEAM" class="pl-10 my-1">
        You can specify users on the form's management screen once created.
      </v-row>
    </v-radio-group>

    <v-checkbox
      v-model="enableSubmitterDraft"
      :disabled="userType === ID_MODE.PUBLIC"
    >
      <template #label>
        <span>
          Enable submitters of this form to
          <strong>Save and Edit Draft</strong> submissions
        </span>
      </template>
    </v-checkbox>

    <v-checkbox v-model="enableStatusUpdates">
      <template #label>
        <span>
          Enable submission <strong>Status Updates</strong> for this form (i.e.
          Submitted, Assigned, Completed)
        </span>
      </template>
    </v-checkbox>

    <v-checkbox v-model="showSubmissionConfirmation">
      <template #label>
        Show the submission confirmation details
        <v-tooltip bottom>
          <template v-slot:activator="{ on, attrs }">
            <v-icon color="primary" class="ml-3" v-bind="attrs" v-on="on">
              help_outline
            </v-icon>
          </template>
          <span>
            Selecting this option controls what the submitting user of this form
            will see on successful submission. <br />
            If checked, it will display
            <ul>
              <li>the Confirmation ID</li>
              <li>
                the option for the user to email themselves a submission
                confirmation
              </li>
            </ul>
          </span>
        </v-tooltip>
      </template>
    </v-checkbox>

    <v-checkbox v-model="sendSubRecieviedEmail">
      <template #label>
        Send my team a notification email when a user submits our form
        <v-tooltip bottom>
          <template v-slot:activator="{ on, attrs }">
            <v-icon color="primary" class="ml-3" v-bind="attrs" v-on="on">
              help_outline
            </v-icon>
          </template>
          <span>
            Send a notification to your specified email address when any user
            submits this form
          </span>
        </v-tooltip>
      </template>
    </v-checkbox>

    <v-combobox
      v-if="sendSubRecieviedEmail"
      v-model="submissionReceivedEmails"
      :rules="emailArrayRules"
      dense
      flat
      solid
      outlined
      hide-selected
      clearable
      hint="Add one or more valid email addresses"
      label="Notification Email Addresses"
      multiple
      small-chips
      deletable-chips
      :delimiters="[' ', ',']"
      append-icon=""
    >
      <template v-slot:no-data>
        <v-list-item>
          <v-list-item-content>
            <v-list-item-title>
              Press <kbd>enter</kbd> or <kbd>,</kbd> or <kbd>space</kbd> to add
              multiple email addresses
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </template>
    </v-combobox>
  </v-container>
</template>

<script>
import { mapActions } from 'vuex';
import { mapFields } from 'vuex-map-fields';
import { IdentityMode, IdentityProviders, Regex } from '@/utils/constants';

export default {
  name: 'FormSettings',
  props: {
    formId: String,
  },
  data() {
    return {
      valid: false,
      // Validation
      loginRequiredRules: [
        (v) =>
          v !== 'login' ||
          this.idps.length > 0 ||
          'Please select at least 1 log-in type',
      ],
      descriptionRules: [
        (v) =>
          !v ||
          v.length <= 255 ||
          'Form Description must be 255 characters or less',
      ],
      nameRules: [
        (v) => !!v || 'Form Title is required',
        (v) =>
          (v && v.length <= 255) || 'Form Title must be 255 characters or less',
      ],
      emailArrayRules: [
        (v) =>
          !this.sendSubRecieviedEmail ||
          v.length > 0 ||
          'Please enter at least 1 email address',
        (v) =>
          !this.sendSubRecieviedEmail ||
          v.every((item) => new RegExp(Regex.EMAIL).test(item)) ||
          'Please enter all valid email addresses',
      ],
    };
  },
  computed: {
    ...mapFields('form', [
      'form.description',
      'form.enableSubmitterDraft',
      'form.enableStatusUpdates',
      'form.id',
      'form.idps',
      'form.name',
      'form.sendSubRecieviedEmail',
      'form.showSubmissionConfirmation',
      'form.submissionReceivedEmails',
      'form.userType',
    ]),
    ID_MODE() {
      return IdentityMode;
    },
    ID_PROVIDERS() {
      return IdentityProviders;
    },
  },
  methods: {
    ...mapActions('form', ['fetchForm']),
    userTypeChanged() {
      // if they checked enable drafts then went back to public, uncheck it
      if (this.userType === this.ID_MODE.PUBLIC) {
        this.enableSubmitterDraft = false;
      }
    },
  },
};
</script>
