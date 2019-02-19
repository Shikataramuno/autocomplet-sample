<template>
  <div class="autocomplete">
    <input class="autocomplete-input" type="text"
      v-model="search"
      @input="onChange()"
      @keydown.down="onArrowDown"
      @keydown.up="onArrowUp"
      @keydown.enter="onEnter"/>
    <ul ref="list" class="autocomplete-results" v-show="isOpen">
      <li class="autocomplete-result" v-for="(result, i) in results" :key="i"
        @click="setResult(result)"
        :class="{ 'is-active': i === arrowCounter }">
        {{ result }}
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Emit, Watch, Vue } from 'vue-property-decorator';

@Component
export default class AutoComplete extends Vue {
  // name: string = 'autocomplete';
  @Prop({default: ''}) option!: string;
  @Prop({default: []}) options!: string[];
  @Prop({default: true}) onChangeEvent!: boolean;
  $refs!: {
    'list': HTMLElement;
  };

  search: string = '';
  isOpen: boolean = false;
  results: string[] = [];
  arrowCounter: number = -1;

  onChange(): string {
    // Let's  our flat array
    console.log('onChange : ' + this.search);
    this.filterResults();
    console.log(this.results);
    this.isOpen = true;
    return this.search;
  }

  filterResults(): void {
    // first uncapitalize all the things
    this.results = this.options.filter((option) => {
      return option.indexOf(this.search) > -1;
    });
    this.arrowCounter = -1;
  }

  @Emit('input')
  setResult(result: string): string {
    console.log('setResult');
    this.isOpen = false;
    // Let's warn the parent that a change was made
    this.search = result;
    return result;
  }

  onArrowDown(): void {
    if (this.arrowCounter < this.results.length - 1) {
      this.arrowCounter = this.arrowCounter + 1;
      const scrollTop = (this.arrowCounter - 3) * 32;
      if (this.$refs.list.scrollTop < scrollTop) {
        this.$refs.list.scrollTop = scrollTop;
      }
    }
  }

  onArrowUp(): void {
    if (this.arrowCounter > 0) {
      this.arrowCounter = this.arrowCounter - 1;
      if (this.$refs.list.scrollTop > this.arrowCounter * 32) {
        this.$refs.list.scrollTop = this.arrowCounter * 32;
      }
    }
  }

  @Emit('input')
  onEnter(): string {
    // console.log('this.arrowCounter : ' + this.arrowCounter)
    console.log(`onEnter`);
    if (this.arrowCounter >= 0) {
      // Let's warn the parent that a change was made
      const ret = this.results[this.arrowCounter];
      this.isOpen = false;
      this.arrowCounter = -1;
      return ret;
    } else {
      if (this.search !== '') {
        this.isOpen = false;
        this.arrowCounter = -1;
      }
      return this.search;
    }
  }

  handleClickOutside(evt: any): void {
    if (!this.$el.contains(evt.target)) {
      if (this.isOpen && !this.onChangeEvent) {
        this.search = this.option;
      }
      this.isOpen = false;
      this.arrowCounter = -1;
    }
  }

/*
  @Watch('options', { immediate: true, deep: true })
  onItemsChanged(newVal: string[], oldVal: string[]): void {
    // actually compare them
    console.log(newVal);
    console.log(oldVal);
    if (oldVal && newVal.length !== oldVal.length) {
      this.results = newVal;
    }
  }
*/

  @Watch('option')
  onOptionChanged(newVal: string, oldVal: string): void {
    this.search = newVal;
  }
  mounted(): void {
    // console.log('Autocomplete.mounted')
    this.search = this.option;
    document.addEventListener('click', this.handleClickOutside);
  }

  destroyed(): void {
    document.removeEventListener('click', this.handleClickOutside);
  }
}
</script>

<style scoped>
.autocomplete {
  width: 100%;
  position: relative;
}
.autocomplete-input {
  padding: 0;
  margin: 0;
  border-color: #eee;
  height: 100%;
  overflow: auto;
  width: 100%;
}
.autocomplete-results {
  padding: 0;
  margin: 0;
  border: 1px solid #eeeeee;
  max-height: 128px;
  overflow: auto;
  width: 100%;
  background-color: #FFFFFF;
}
.autocomplete-result {
  list-style: none;
  text-align: left;
  height: 32px;
  line-height: 32px;
  cursor: pointer;
}
.autocomplete-result.is-active{
  background-color: #4AAE9B;
  color: white;
}
</style>
