<template>
  <div class="parent-view">
    <label>自動補間サンプル</label>
    <autocomplete
      :option="option"
      :options="options"
      :onChangeEvent=false
      @input="optionSelected">
    </autocomplete>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Autocomplete from './Autocomplete.vue';

@Component({
  components: {
    Autocomplete,
  },
})
export default class ParentView extends Vue {
  name: string = 'ParentView';
  options: string[] = [];
  option: string = '';

  optionSelected(option: string): void {
    this.option = option;
  }
  created(): void {
    console.log('ParentView creates');
    this.options = [
      'aaaa',
      'aaab',
      'aabb',
      'abbb',
      'bbbb',
      'bbaa',
      'cccc',
      'dddd',
      'ああああ',
      'おおおお',
      'あい～ん',
      'あかーん',
      'あおーい',
      'あした',
      'あさって',
    ];
  }
}
</script>

<style scoped>
.parent-view {
  width: 300px;
  margin-left: 20px;
  text-align: left;
}
</style>
