<template>
  <section class="wrapper">
    <b-table
      :data="movies"
      :loading="isLoading"

      paginated
      backend-pagination
      :total="total"
      :per-page="perPage"
      @page-change="onPageChange"

      backend-sorting
      :default-sort-direction="defaultSortOrder"
      :default-sort="[sortField, sortOrder]"
      @sort="onSort">
      <template slot-scope="props">
        <b-table-column class="movie-poster">
          <img :src="`https://image.tmdb.org/t/p/w150${props.row.poster_path}`" :alt="props.row.title">
        </b-table-column>
        <b-table-column class="movie-title" field="title" label="Film Adı">
          <div>{{props.row.title}}</div>
          <span class="tag is-light">{{ props.row.release_date | moment }}</span>
        </b-table-column>
        <b-table-column field="vote_average" label="Puanı" numeric>
          {{props.row.vote_average}}
        </b-table-column>
        <b-table-column field="vote_count" label="Oy Sayısı" numeric sortable>
          {{props.row.vote_count}}
        </b-table-column>
        <b-table-column class="has-text-right" label="Oy Ver">
          <a class="button" @click="props.row.vote_count++">
            <span class="icon is-small">
              <i class="fa fa-angle-up fa-lg"></i>
            </span>
          </a>          
          <a class="button" @click="props.row.vote_count--">
            <span class="icon is-small">
              <i class="fa fa-angle-down fa-lg"></i>
            </span>
          </a>          
        </b-table-column>
      </template>        
    </b-table>
  </section>
</template>

<script>
import moment from "moment";
moment.locale("tr");

export default {
  name: 'app',
  data () {
    return {
      movies: [],
      isLoading: false,
      perPage: 20,
      sortField: 'vote_count',
      sortOrder: 'desc',
      defaultSortOrder: 'desc',      
      page: 1,
      total: 0,
    }
  },
  methods: {
    loadData() {
      const url = `https://api.themoviedb.org/3/discover/movie?api_key=c8cdc0a1e14498b0f1c001c170563b44&language=tr-TR&sort_by=${this.sortField}.${this.sortOrder}&page=${this.page}`;
      this.isLoading = true    
      fetch(url)
      .then((res) => { return res.json()  } )
      .then((res) => {
        this.res = []

        let currentTotal = res.total_results
        if (res.total_results / this.perPage > 1000) {
          currentTotal = this.perPage * 1000
        }
        this.total = currentTotal

        let movies = [];
        res.results.forEach((item) => movies.push(item))
        this.movies = movies;

        this.isLoading = false;
      })
    },
    onSort(field, order) {
      this.sortField = field
      this.sortOrder = order
      this.loadData()
    },    
    onPageChange(page) {
      this.page = page
      this.loadData()
    }
  },
  filters: {
    moment: (date) => {
      return moment(date).format('Y');
    }
  },  
  created() {
    this.loadData();
  },
}
</script>

<style lang="scss">

  body {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    padding-top:15px;
  }
  a {
    color:#2c3e50;
  }

  .wrapper {
    max-width:1170px;
    margin-left:auto;
    margin-right:auto;
    padding-left:15px;
    padding-right:15px;
  }

  //Table Customize
  .b-table {
    .table {
      border-collapse:collapse;
    }
    thead {
      tr {
        th {
          &:nth-child(2) {
            .th-wrap {
              flex-direction: row;
            }
          }
        }
      }
      .th-wrap {
        flex-direction:row-reverse;
      }
    }
    td, th {
      vertical-align:middle;
      .tag {
        margin-top:5px;
      }
    }
  }

  //Pagination Customize
  .pagination-link {
    &.is-current {
      background-color:#2c3e50;
      border-color:#2c3e50;
    }
  }
  .pagination-previous:focus, 
  .pagination-next:focus, 
  .pagination-link:focus {
    border-color:#2c3e50;
  }

  .movie {
    &-poster {
      width:150px;
      @media only screen and (max-width:768px) {
        &:before {
          display:none;
        }
        span {
          display:inline-flex;
          margin-left:auto;
          margin-right:auto;
        }        
      }
    }
    &-title {
      text-align:left;
    }
  }

</style>
