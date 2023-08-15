<template>
  <div class="q-pa-md bg-black">
    <div class="row justify-center items-center">
      <div class="col-10 col-sm-8 col-md-6">
        <q-input  dark color="white" v-model="search_query"  rounded outlined  @keydown.enter.prevent="submitInput(search_query)" label="Search any Anime..." >
          <template v-slot:append>  
            <q-icon name="search" color="white"/>
          </template>
        </q-input>
      </div>
    </div>
    <div class="row justify-center q-mt-md text-white" v-if="data">
      <div v-for="(result, index) in resultArray" :key="index" class="col-10 col-sm-8 col-md-4 q-ma-md">
      <q-card class=" full-height bg-grey-10" >
        <q-card-section>
          <div class="row"><div class="col-12 ellipsis"><strong>{{ result['title'] }}</strong></div></div>
            
            <hr /><br />
          
          <q-img :src="result['img-url']" :ratio="16/9"/>
          <p class="q-pt-md"><b>Storyline:</b><br>{{ result['description'].slice(0,100)}}{{  result['description'].length >100 ? "..." : "" }}</p>
        </q-card-section>
        <q-table class="q-pa-md bg-grey-10 text-white" :hide-pagination="true" title="More Info" :columns="columns" :rows="result['rows']" flat></q-table>
      </q-card>
    </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { api } from 'src/boot/axios';

export default {
  name: 'IndexPage',
  setup() {
    const search_query = ref(null);
    const data = ref(null);
    const resultArray = ref([]);
    const columns = [
      {
        name: 'releaseDate',
        field: 'releaseDate',
        label: 'Release Date',
      },
      {
        name: 'Status',
        field: 'Status',
        label: 'Status',
      },
      {
        name: 'TotalEpisodes',
        field: 'TotalEpisodes',
        label: 'Total Episodes',
      },
      {
        name: 'episodeLength',
        field: 'episodeLength',
        label: 'Episode length(mins)',
      },
    ];

    async function submitInput(search_query) {
      const response = await api.get(search_query);
      data.value = response.data;
      console.log(data.value)

      resultArray.value = [];
      for (let i = 0; i < data.value.data.length; i++) {
        const result = {};

        if (data.value.data[i].attributes.titles.en) {
          result['title'] = data.value.data[i].attributes.titles.en;
        } else if (data.value.data[i].attributes.titles.en_us) {
          result['title'] = data.value.data[i].attributes.titles.en_us;
        } else {
          result['title'] = data.value.data[i].attributes.canonicalTitle;
        }

        if (data.value.data[i].attributes.synopsis) {
          result['description'] = data.value.data[i].attributes.synopsis;
        } else if (data.value.data[i].attributes.description) {
          result['description'] = data.value.data[i].attributes.description;
        }
        if(data.value.data[i].attributes.coverImage){
          result['img-url'] = data.value.data[i].attributes.coverImage.original;
        }
        else{
          result['img-url'] = data.value.data[i].attributes.posterImage.original;
        }
        
        result['ep-count'] = data.value.data[i].attributes.episodeCount;
        result['ep-size'] = data.value.data[i].attributes.episodeLength;
        result['release'] = data.value.data[i].attributes.startDate;
        result['status'] = data.value.data[i].attributes.status;

        result['rows'] = [
          {
            releaseDate: result['release'],
            Status: result['status'],
            TotalEpisodes: result['ep-count'],
            episodeLength: result['ep-size'],
          },
        ];

        resultArray.value.push(result);
      }
    }

    return {
      search_query,
      submitInput,
      data,
      resultArray,
      columns,
    };
  },
};
</script>
