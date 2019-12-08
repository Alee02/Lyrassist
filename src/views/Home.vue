<template>
  <div class="home">
    <v-container fluid>
      <!--            <div v-for="i in 16" :key="i">-->
      <!--                <v-text-field>{{i}}</v-text-field>-->
      <!--            </div>-->
      <h1>Lyr-assist</h1>
      <v-btn @click="editMode=!editMode">Edit Mode</v-btn>
      <RhymeSearcher v-if="false"></RhymeSearcher>
<!--      <v-text-field v-model="text">-->
<!--      </v-text-field>-->
<!--      <v-btn @click="hyphenate_en">Do it</v-btn>-->
      <div class="container">
        <p lang="en-us">For which reason the Helvetii also surpass the rest of the Gauls in valor, as they contend with the Germans in almost daily battles, when they either repel them from their own territories, or themselves wage war on their frontiers.</p>
      </div>
      <audio controls @timeupdate="onTime">
        <source src="../assets/Eminem_-_Kick_Off_Freestyle_.mp3" type="audio/mpeg">
      </audio>
      <Board id="board-1">
        <Card id="card-1" draggable="true">
          <p>Card One</p>
        </Card>
      </Board>
      <Board id="board-2">
        <Card id="card-2" draggable="true">
          <p>Card Two</p>
        </Card>
      </Board>

<!--      <v-row>-->
<!--        <v-col>-->
<!--          <draggable v-model="myArray" group="people" @start="drag=true" @end="drag=false" handle=".handle">-->
<!--            <div v-for="element in myArray" :key="element.id">-->
<!--              <v-text-field-->
<!--                :disabled="editMode"-->
<!--                :value="editMode ? '' : element.bar"-->
<!--              >-->
<!--                <template v-slot:prepend v-if="!editMode">-->
<!--                  &lt;!&ndash;                    <v-icon class="handle">mdi-menu</v-icon>&ndash;&gt;-->
<!--                  <v-menu>-->
<!--                    <template v-slot:activator="{ on }">-->
<!--                      <v-icon v-on="on" class="handle">mdi-menu</v-icon>-->
<!--                    </template>-->
<!--                    <v-card>-->
<!--                      <v-card-text class="pa-6">-->
<!--                        <v-btn>-->
<!--                          <v-icon left>mdi-target</v-icon>-->
<!--                          Click me-->
<!--                        </v-btn>-->
<!--                      </v-card-text>-->
<!--                    </v-card>-->
<!--                  </v-menu>-->
<!--                </template>-->
<!--                <template v-if="editMode" v-slot:prepend-inner>-->
<!--&lt;!&ndash;                  <draggable group="people" @start="drag=true" @end="drag=false" horizontal>&ndash;&gt;-->
<!--                  <v-tooltip bottom>-->
<!--                    <template v-slot:activator="{ on }">-->
<!--                      <v-chip-->
<!--                        v-for="i in element.bar.split(' ')"-->
<!--                        :key="i"-->
<!--                        v-bind="attrs"-->
<!--                        class="ma-1"-->
<!--                        small-->
<!--                        :color="`${element.color} lighten-3`"-->
<!--                        label-->
<!--                        v-on="on"-->
<!--                      >-->
<!--                                            <span class="pr-2">-->
<!--                                                {{i}}-->
<!--                                            </span>-->
<!--                      </v-chip>-->
<!--                    </template>-->
<!--                    <span>Tooltip</span>-->
<!--                  </v-tooltip>-->
<!--&lt;!&ndash;                  </draggable>&ndash;&gt;-->
<!--                  &lt;!&ndash;                                    </draggable>&ndash;&gt;-->
<!--                </template>-->
<!--              </v-text-field>-->
<!--            </div>-->
<!--          </draggable>-->
<!--        </v-col>-->
<!--      </v-row>-->
      <draggable></draggable>
    </v-container>
  </div>
</template>

<script>
import Board from '../components/Board'
import Card from '../components/Card.vue'
import RhymeSearcher from '../components/RhymeSearcher/RhymeSearcher.vue'
const draggable = require('vuedraggable')
const hyphenopoly = require('hyphenopoly')
// var path = require('path')

export default {
  name: 'home',
  components: {
    draggable,
    Board,
    Card,
    RhymeSearcher
  },
  created: () => {
  },
  data: () => ({
    // lyrics: {
    //   lintes: [
    //     {
    //
    //     }
    //   ]
    // },
    audioFile: null,
    text: 'Highlight me',
    myArray: [
      { id: '1', bar: 'Ayo, my pen and paper cause a chain reaction', color: 'green' },
      { id: '2', bar: 'To get your brain relaxing, the zany-acting maniac in action', color: 'green' },
      { id: '3', bar: 'A brainiac in fact son, you mainly lack attraction', color: 'green' },
      { id: '4', bar: 'You look insanely wack when just a fraction of my tracks run', color: 'green' },
      { id: '5', bar: 'My rhyming skills got you climbing hills', color: 'green' },
      { id: '6', bar: 'I travel through your mind into your spine like siren drills', color: 'green' },
      { id: '7', bar: `I'm sliming grills of roaches, with spray that disinfects`, color: 'green' },
      { id: '8', bar: `and twist the necks of rappers 'til their spinal column disconnects`, color: 'green' },
      { id: '9', bar: `Put this in decks and check the monologue, turn your system up`, color: 'green' },
      { id: '10', bar: 'Twist them up, and indulge in the marijuana smoke', color: 'green' },
      { id: '11', bar: 'This is the season for noise pollution contamination', color: 'green' },
      { id: '12', bar: 'Examination of more car tunes than animation', color: 'green' },
      { id: '13', bar: 'My lamination of narration', color: 'green' },
      { id: '14', bar: 'Hits a snare and bass in a track for duck rapper interrogation', color: 'green' },
      { id: '15', bar: `When I declare invasion, there ain't no time to be staring gazing`, color: 'green' },
      { id: '16', bar: 'I turn the stage into a barren wasteland...', color: 'green' },
      { id: '17', bar: `I'm infinite`, color: 'green' }
    ],
    editMode: false,
    colors: ['green', 'purple', 'indigo', 'cyan', 'teal', 'orange'],
    dragging: false,
    enabled: true,
    hyphenator: null
  }),
  methods: {
    formatTime (secs) {
      const secsNum = parseInt(secs.toString(), 10)
      const hours = Math.floor(secsNum / 3600) % 24
      const minutes = Math.floor(secsNum / 60) % 60
      const seconds = secsNum % 60

      return `${hours}:${minutes}:${seconds}`
      // [hours, minutes, seconds]
      //   .map((num) => (num < 10)
      //     ? '0' + num
      //     : num
      //   )
      //   .filter((num, i) => (
      //     (num !== '00') || (i > 0))
      //   )
      //   .join(':')
    },
    onTime (e) {
      // console.log(e)
      // console.log(e.target.currentTime)
      console.log(e.target.currentTime)
    },
    async hyphenate_en () {
      const hyphenator = hyphenopoly.config({
        'require': ['de', 'en-us'],
        'hyphen': '•',
        'exceptions': {
          'en-us': 'en-han-ces'
        }
      })
      const hyphenateText = await hyphenator.get('en-us')
      console.log(hyphenateText('hyphenate'))
    },
    findSyllables (word) {
      console.log(`Finding Syllables for ${word}`)
      /* eslint-disable no-new */
      return new Promise((resolve, reject) => {
        fetch(`https://api.datamuse.com/words?sp=${word}&md=sfprd`)
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
    }
  },
  watch: {
    // audioFile (val) {
    //   console.log('Audio file changed')
    //   val.ontimeupdate(val, 'timeupdate')
    //     .subscribe((e) => {
    //       console.log(e.currentTime)
    //     })
    // },
    text (text) {
      // eslint-disable-next-line valid-typeof
      if (typeof text === null) {
        console.log('Object')
        console.log(text)
      } else if (typeof text === 'string') {
        console.log('String')
        console.log(text)
        const wordArray = text.split(' ')
        console.log(wordArray)
        const wordWithMeta = wordArray.map((word) => {
          return this.findSyllables(word)
        })
        console.log('Printing Word Array')
        console.log(wordWithMeta)
        Promise.all(wordWithMeta).then(arr => {
          console.log(arr)
          arr.forEach(e => {
            e.tags.forEach(tag => {
              if (tag.includes('pron')) {
                console.log(tag)
                console.log(tag.trim().split(' ').filter(e => !(e.includes('pron') || e === ' ')))
              }
            })
          })
        })
      } else {
        console.log('Not String or Object')
      }
    }
  }
}
</script>
