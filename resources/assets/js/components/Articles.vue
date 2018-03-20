<template>
  <div>
      <h2>Articles</h2>
      <div class="card card-body mb-2" v-for="article in articles" v-bind:key="article.id">
        <h3>{{ article.title }}</h3>
        <p>{{ article.body }}</p>
      </div>
  </div>
</template>

<script>
  export default {
    data() { // criando o objeto de dados
      return {
        articles: [], // array que receberá os registros
        article: { // objeto com os campos q serao alimentados com dados
          id: '',
          title: '',
          body: ''
        },
        article_id: '', // variável usada como parâmetro no update, pq é usado o mesmo método para inclusão (store)
        pagination: {},
        edit: false // update é false por padrão, pq é usado o mesmo form para inclusão e edição
      }
    },

    // método para buscar os dados automaticamente ao carregar a página
    created() {
      this.fetchArticles(); // o método deve ser definido dentro objeto Methods
    },

    methods: {
      fetchArticles() {
        // usando fetch API
        fetch('api/articles')
          .then(res => res.json())
          .then(res => {
            //console.log(res.data);
            // atribuindo os registros no array articles para serem exibidos na tela
            this.articles = res.data;
          })
      }
    }
  }
</script>
