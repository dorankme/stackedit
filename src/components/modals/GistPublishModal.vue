<template>
  <div class="modal__inner-1" role="dialog" aria-label="Publish to Gist">
    <div class="modal__inner-2">
      <div class="modal__image">
        <icon-provider provider-id="gist"></icon-provider>
      </div>
      <p>This will publish <b>{{currentFileName}}</b> to a <b>Gist</b>.</p>
      <form-entry label="Filename" error="filename">
        <input slot="field" class="textfield" type="text" v-model.trim="filename" @keyup.enter="resolve()">
      </form-entry>
      <div class="form-entry">
        <div class="form-entry__checkbox">
          <label>
            <input type="checkbox" v-model="isPublic"> Public
          </label>
        </div>
      </div>
      <form-entry label="Existing Gist ID (optional)">
        <input slot="field" class="textfield" type="text" v-model.trim="gistId" @keyup.enter="resolve()">
        <div class="form-entry__info">
          If the file exists in the Gist, it will be replaced.
        </div>
      </form-entry>
      <form-entry label="Template">
        <select slot="field" class="textfield" v-model="selectedTemplate" @keyup.enter="resolve()">
          <option v-for="(template, id) in allTemplates" :key="id" :value="id">
            {{ template.name }}
          </option>
        </select>
        <div class="form-entry__actions">
          <a href="javascript:void(0)" @click="configureTemplates">Configure templates</a>
        </div>
      </form-entry>
      <div class="modal__info">
        <b>ProTip:</b> You can provide a value for <code>title</code> in the <a href="javascript:void(0)" @click="openFileProperties">file properties</a>.
      </div>
      <div class="modal__button-bar">
        <button class="button" @click="config.reject()">Cancel</button>
        <button class="button" @click="resolve()">Ok</button>
      </div>
    </div>
  </div>
</template>

<script>
import gistProvider from '../../services/providers/gistProvider';
import modalTemplate from './modalTemplate';

export default modalTemplate({
  data: () => ({
    filename: '',
    gistId: '',
  }),
  computedLocalSettings: {
    isPublic: 'gistIsPublic',
    selectedTemplate: 'gistPublishTemplate',
  },
  created() {
    this.filename = `${this.currentFileName}.md`;
  },
  methods: {
    resolve() {
      if (!this.filename) {
        this.setError('filename');
      } else {
        // Return new location
        const location = gistProvider.makeLocation(
          this.config.token, this.filename, this.isPublic, this.gistId);
        location.templateId = this.selectedTemplate;
        this.config.resolve(location);
      }
    },
  },
});
</script>
