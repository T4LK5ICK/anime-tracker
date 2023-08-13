<template>
  <div class="q-pa-md ">
    <div class="row justify-center items-center">
          <div class="col-10  col-sm-8 col-md-6">
              <q-input  v-model="search_query" outlined @keydown.enter.prevent="submitInput(search_query)"
                label="search any anime..."><template v-slot:append>
                  <q-icon name="search" />
                </template></q-input> 
      </div>
    </div>
    <div class="row justify-center q-mt-md" v-if="data">
      <q-card class="col-10 col-sm-8 col-md-6">
        
        <q-card-section>
          <q-card-title class="text-bold" style="font-size: x-large;">
            {{ result['title'] }}
            <hr><br>
          </q-card-title>
          <q-img :src="result['img-url']" :ratio="16 / 9" />
          <p class="q-pt-md"><b>Storyline:</b><br>{{ result['description'] }}</p>

        </q-card-section>
        <q-table class="q-pa-md" :hide-pagination="true" title="More Info" :columns="columns" :rows="rows" flat></q-table>
      </q-card>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { api } from 'src/boot/axios'




export default {
  name: 'IndexPage',
  setup() {

    const search_query = ref(null)
    const data = ref(null)
    const result = ref({})
    // const columns = [
    //   { label: 'Release Date', name: 'ReleaseDate' },
    //   { label: 'Status', name: 'Status' },
    //   { label: 'Total Episodes', name: 'TotalEpisodes' },
    //   { label: 'episode length(mins)', name: 'episodeLength' },
    // ]
    const rows = ref([])
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
      

    ]
    



    async function submitInput(search_query) {
      const response = await api.get(search_query)
      data.value = response.data
      console.log(data.value)
      result.value['title'] = data.value.data[0].attributes.titles.en
      result.value['description'] = data.value.data[0].attributes.synopsis
      result.value['img-url'] = data.value.data[0].attributes.coverImage.original
      result.value['ep-count'] = data.value.data[0].attributes.episodeCount
      result.value['ep-size'] = data.value.data[0].attributes.episodeLength
      result.value['release'] = data.value.data[0].attributes.startDate
      result.value['status'] = data.value.data[0].attributes.status

      rows.value = [
        {
          releaseDate: result.value['release'], Status: result.value['status'], TotalEpisodes: result.value['ep-count'], episodeLength: result.value['ep-size']
        },
      ]
    }


    return {
      search_query, submitInput, data, result, columns, rows
    }
  }


}


</script>
