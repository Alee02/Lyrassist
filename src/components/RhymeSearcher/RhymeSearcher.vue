<template>
    <div class="RhymeSearcher">
      <v-card
        color="black lighten-2"
        dark
      >
        <v-card-title class="headline black lighten-3">
          Lyrassist Rhyme Searcher v.0.1
        </v-card-title>
        <v-card-text>
          Search For Some Rhymes
        </v-card-text>
        <v-card-text>
          <v-autocomplete
            v-model="model"
            :items="wordSuggestions"
            :loading="isLoading"
            :search-input.sync="search"
            color="white"
            hide-no-data
            hide-selected
            item-text="word"
            item-value="word"
            label="Lyrassist Rhyme Api"
            placeholder="Start typing to Search"
            prepend-icon="mdi-database-search"
            multiple
            chips
            return-object
          ></v-autocomplete>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            :disabled="!model"
            color="grey darken-3"
            @click="model = null"
          >
            Clear
            <v-icon right>mdi-close-circle</v-icon>
          </v-btn>
        </v-card-actions>
      </v-card>
      <v-expand-transition v-if="rhymesArray.length > 0">
        <v-list class="black lighten-3">
          <v-list-item
            v-for="(rArray, i) in rhymesArray"
            :key="i"
          >
            <v-list-item-content>
              <v-list-item-title class="white--text">
                {{rArray.map((e) => (e.word + ` (${e.syl}) `)).join(" ")}}
              </v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-expand-transition>
    </div>
</template>

<script>
const _ = require('lodash')
//
// function RhymeSuggestion (rhymeArray, word, syl) {
//   this.rhymeArray = rhymeArray
//   this.word = word
//   this.syl = syl
// }

export default {
  props: ['word'],
  data: () => ({
    descriptionLimit: 60,
    wordSuggestions: [],
    isLoading: false,
    model: null,
    search: null,
    wordsWithMetaData: [],
    rhymesArray: []
  }),
  computed: {
    fields () {
      if (!this.model) return []

      return Object.keys(this.model)
    }
  },
  methods: {
    findSyllables (word) {
      console.log('Finding Syllables')
      /* eslint-disable no-new */
      return new Promise((resolve, reject) => {
        fetch(`https://api.datamuse.com/words?sp=${word}&md=dpsrf`)
          .then(res => res.json())
          .then(res => {
            if (res.length > 0) {
              resolve(res[0])
            } else {
              reject(res)
            }
          })
          .catch(err => {
            console.log(err)
          })
      })
    },
    getSuggestions (searchQuery, max) {
      fetch(`http://api.datamuse.com/sug?s=${searchQuery}&max=${max}`)
        .then(res => res.json())
        .then(listOfSuggestions => {
          console.log(`%c Searching for ${searchQuery} in /sug`, 'background: #222; color: #bada55')
          console.log(listOfSuggestions)
          // this.count = count
          this.wordSuggestions = listOfSuggestions
        })
        .catch(err => {
          console.error(err)
        })
        .finally(() => (this.isLoading = false))
    },
    searchWord: _.debounce(function (searchQuery) {
      console.log('Search Word')
      // (val) => {
      console.log('Searching word..')
      console.log(searchQuery)
      // Items have already been loaded
      // if (this.items.length > 0) return val
      // Items have already been requested
      // if (this.isLoading) return
      console.log('Search Input Changed')
      console.log(searchQuery)
      if (typeof searchQuery === 'object' && searchQuery !== null) {
        const multi = searchQuery.split(' ')
        if (multi.length > 1) {
          console.log('In Search: Multi ' + multi)
          var wordArray = multi.map(word => {
            /* eslint-disable no-new */
            return this.findSyllables(word)
          })
          Promise.all(wordArray).then(a => {
            this.wordsWithMetaData = a
          })
        } else {
          console.log('Muli in searchWord')
          console.log(searchQuery)
        }
      } else if (typeof searchQuery === 'string') {
        console.log('Searching When its string')
        console.log('Here after checking length')
        // this.isLoading = true
        // Lazily load input items
        // fetch(`http://api.datamuse.com/words?rel_rhy=${val}&max=5`)
        this.model = searchQuery
        // this.getSuggestions(searchQuery, 5)
      } else {
        this.rhymesArray = []
        console.log('Search Input Null')
        console.log(searchQuery)
      }
      // }, 5)
    }, 500),
    getRhymes (word, index, max) {
      console.log(`Finding rhyme for ${word}`)
      return new Promise((resolve, reject) => {
        fetch(`http://api.datamuse.com/words?rel_rhy=${word}&max=${max}`)
          .then(res => res.json())
          .then(res => {
            console.log('Found rhymes in model watcher')
            console.log(word)
            console.log('Setting Items')
            console.log(res)
            res.map(w => {
              console.log(w)
              return w
            })
            console.log('After w map')
            const rhymeObjArray = res.map((wordObj) => {
              return {
                index: index,
                word: wordObj.word,
                score: wordObj.score,
                syl: wordObj.numSyllables
              }
            })
            console.log(rhymeObjArray)
            resolve(rhymeObjArray)
          })
          .catch(err => {
            reject(err)
          })
          .finally(() => (this.isLoading = false))
      })
    }
  },
  watch: {
    rhymesArray (val) {
      console.log('rhymesArray updated')
      console.log(val)
    },
    model (searchString) {
      console.log('Model changed')
      if (typeof searchString !== 'undefined' || searchString !== null) {
        const multi = searchString.split(' ')
        if (multi.length > 1) {
          console.log('String is multiple in Model')
          console.log(multi)
          var wordArray = multi.map(word => {
            /* eslint-disable no-new */
            return this.findSyllables(word)
          })
          Promise.all(wordArray).then(a => {
            console.log('Setting wordsWithMetaData')
            this.wordsWithMetaData = a
          })
        } else {
          console.log(`Model is ${searchString}`)
          this.getRhymes(searchString, 0, 5)
        }
      } else {
        console.log('Model found')
        console.log(searchString)
      }
    },
    wordsWithMetaData (val) {
      console.log('Word Array with meta data changed')
      console.log(val)
      if (val) {
        console.log(val.map((w) => {
          return w.word + `(${w.numSyllables})`
        }))
        const rhymeObjects = val.map((wordObject, index) => {
          return this.getRhymes(wordObject.word, index, 5)
        })
        console.log('GETTING ALL RHYMES')
        Promise.all(rhymeObjects).then(arr => {
          this.rhymesArray = arr
          // console.log(arr)
        })
        // const rhymeSuggestionArray = val.map((word) => {
        //   return RhymeSuggestion([], word, word.numSyllables)
        // })
        // console.log('Rhyme suggestion array')
        // console.log(rhymeSuggestionArray)
        // this.wordsWithMetaData = rhymeSuggestionArray
      }
    },
    search (val) {
      this.searchWord(val)
    }
  }
}
</script>
