<template>
  <div id="competence-breakdown" :class="{['comp-area-'+competenceArea.id]:true, open:menuIsOpened}">
    <header class="mdc-toolbar mdc-elevation--z4 bg">
      <div class="mdc-toolbar__row">
        <section class="mdc-toolbar__section mdc-toolbar__section--align-start">
          <button @click="clickMenu()" class="demo-menu material-icons mdc-toolbar__menu-icon">menu</button>
          <span class="mdc-toolbar__title catalog-title">{{competence.name}}</span>
        </section>

        <!--    <section class="mdc-toolbar__section mdc-toolbar__section&#45;&#45;align-end" role="toolbar">
              <a href="/index.html" class="material-icons mdc-toolbar__icon" aria-label="Home" alt="Home">home</a>
            </section>-->


      </div>
    </header>
    <main class="competence-main">
      <h1 class="mdc-typography--display1 color ">{{competence.name}}</h1>
      <h2 class="mdc-typography--title ">{{competence.hint}}</h2>
      <div class="mdc-layout-grid competences-container">
        <div class="mdc-layout-grid__inner">
          <div class="mdc-layout-grid__cell mdc-layout-grid__cell--span-12">
            <div class="competences bl-color">
              <h1>Descriptior </h1>
              <ul>
                <li class="color" v-for="descriptor in competence.descriptors">&#183; {{descriptor}}</li>
              </ul>
            </div>
            <div class="table-responsive">
              <table class="table table-bordered">
                <thead v-model="collapse" v-on:click="collapse = !collapse" lass="clickable" data-toggle="collapse" data-target="#progression-model" aria-expanded="false" aria-controls="progression-model">
                  <tr>
                    <th class="text-center"><p>THREAD</p><i aria-hidden="true" class="collapse-icon material-icons arrow"><span v-if="collapse">unfold_more</span><span v-else>unfold_less</span></i></th>
                    <td class="text-center" scope="col" colspan="2" v-for="(category, index) in progressionCategories" :class="levelBGColor[index*2]" data-toggle="tooltip" data-placement="top" title="Click here to see detail."><p class="text-uppercase"><strong>{{category.title}}</strong></p>{{category.description}}</td>
                  </tr>
                </thead>
                <tbody id="progression-model" class="collapse">
                  <tr>
                    <th rowspan="2">PROGRESSION MODEL</th>
                    <td scope="col" class="mdc-layout-grid__cell mdc-layout-grid__cell--span-1" v-for="(level, index) in progressionLevels" :class="levelBGColor[index]">{{level.name}}</td>
                  </tr>
                  <tr>
                    <td scope="col" v-for="(level, index) in progressionLevels" :class="levelBGColor[index]"><strong>{{level.level}}. {{level.action}}</strong><br/>{{level.levelDescription}}</td>
                  </tr>
                </tbody>
                <tbody>
                  <tr v-for="competence in competence.threads">
                    <th scope="row" style="width: 10%">{{competence.name}}</th>
                    <td style="width: 11.25%" v-for="(level,index) in competence.levels"  :class="levelBGColor[index]">{{level.name}}<i aria-hidden="true" class="material-icons arrow cell-icon" v-if="level.name ===''">arrow_right_alt</i></td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
          <!-- <div id="competences-chart-container" class="mdc-layout-grid__cell mdc-layout-grid__cell--span-6"></div> -->
        </div>
      </div>
    </main>
    <comp-footer v-bind:competence="competence" v-bind:competence-area="competenceArea"></comp-footer>
  </div>
</template>

<script>
  import eventBus from '../eventBus/eventBus.js'
  import extractCompetencesFromUrlMixin from '../mixIns/extractCompetencesFromUrlMixin.js'
  import footer from './CompetencesFooter.vue'
  import CirclesChart from '../circles-chart.js'
  import {MDCDialog} from '@material/dialog';
  import {progressionCategories, progressionLevels} from '../data/progressionModelCategories.js';

  // let circlesChart = new CirclesChart('competences-chart-container'); // remove Circle Chart

  export default {
    mixins: [extractCompetencesFromUrlMixin],
    components: {
      "comp-footer": footer
    },
    data () {
      return {
        collapse: true,
        competence: null,
        competenceArea: null,
        progressionCategories: null,
        progressionLevels: null,
        menuIsOpened: true,
        levelBGColor: ['foundation','foundation','intermediate','intermediate','advanced','advanced','expert','expert']
      }
    },
    methods: {
      clickMenu()
      {
        eventBus.$emit('toggle-menu');
      },
      clickMenu()
      {
        eventBus.$emit('toggle-menu');
      }
    },
    mounted(){
      // Remove Circle chart
      // console.log("view is mounted");
      // circlesChart.drawChart();
      // circlesChart.select(this.competence.id, 'competence'); // select only competences and not areas

      // const dialog = new MDCDialog(document.querySelector('#mdc-dialog'));
      // document.querySelector('#assess-tool').addEventListener('click', function (evt) {
      //   dialog.lastFocusedTarget = evt.target;
      //   dialog.show();
      // });
    },
    created()
    {
      let urlInfo = this.findCompetenceFromUrl();
      this.competence = urlInfo.competence;
      this.competenceArea = urlInfo.competenceArea;
      this.progressionCategories = progressionCategories;
      this.progressionLevels = progressionLevels;

      // eventBus.$on("MDCPersistentDrawer:open", () => {
      //   this.menuIsOpened = true;
      //   setTimeout(function() {
      //     circlesChart.resizeChart()
      //   }, 50)
      // });
      // eventBus.$on("MDCPersistentDrawer:close", () => {
      //   this.menuIsOpened = false;
      //   setTimeout(function() {
      //     circlesChart.resizeChart()
      //   }, 50)
      // });
      eventBus.$on("competence-changed", (comp) => {
        this.competence = comp;
        this.competenceArea = this.findCompetenceAreaByCompetence(comp);
        // circlesChart.select(comp.id, 'competence'); // select only competences and not areas
      });
    }
  }
</script>

<style lang="scss" scoped>
  @import "../../scss/config/colors";

  .competences-container {
    padding: 0;
  }

  #assess-tool {
    margin-top: 40px;
  }
  .mdc-toolbar, .mdc-toolbar__row {
    min-height: 56px;
  }

  #progression-model {
    border-top: 2px double black;
    border-bottom: 2px double black;
  }

  .collapse-icon {
    font-size: 1.5em;
    margin-top: -0.1em;
  }


  .competences {
    border-left: 5px solid;
    padding-left: 20px;
    margin-top: 20px;
    margin-bottom: 30px;

    h1 {
      margin: 0;
      font-size: 15px;
      line-height: 16px;
      padding-bottom: 12px;
      font-weight: 400;
      color: #757575;
    }

    ul {
      list-style: none;
      margin-top: 0;
      padding-left: 0;
    }

    li {
      font-size: 20px;
      line-height: 40px;
    }
  }

  .mdc-toolbar__title {
    font-weight: 400;
  }

  .mdc-typography--title {
    font-weight: 400;
  }

  .comp-area-1 {
    .bg {
      background-color: $ideasAndOpportunitiesColor;
    }
    .color {
      color: $ideasAndOpportunitiesColor;
    }
    .bl-color {
      border-left-color: $ideasAndOpportunitiesColor;
    }
    .foundation {
      background-color: #E6ECEE;
    }
    .intermediate {
      background-color: #BFCED2;
    }
    .advanced {
      background-color: #9AB6BB;
    }
    .expert {
      background-color: #78A0A7;
    }
  }

  .comp-area-2 {
    .bg {
      background-color: $resourcesColor;
    }
    .color {
      color: $resourcesColor;
    }
    .bl-color {
      border-left-color: $resourcesColor;
    }
    .foundation {
      background-color: #FBEBDF;
    }
    .intermediate {
      background-color: #F3CCAE;
    }
    .advanced {
      background-color: #EBAE82;
    }
    .expert {
      background-color: #E4925B;
    }
  }

  .comp-area-3 {
    .bg {
      background-color: $introActionColor;
    }
    .color {
      color: $introActionColor;
    }
    .bl-color {
      border-left-color: $introActionColor;
    }
    .foundation {
      background-color: #EDF3E7;
    }
    .intermediate {
      background-color: #D3E1C1;
    }
    .advanced {
      background-color: #B8D09C;
    }
    .expert {
      background-color: #9EC27A;
    }
  }
</style>
