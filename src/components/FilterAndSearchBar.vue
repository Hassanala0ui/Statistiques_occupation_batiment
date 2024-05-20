<template>
    <div class="input-group_mb-3">
      <div class="btn-group" role="group">
        <button
          class="text-danger"
          @click="filterStatus = 'Occupé'; $emit('filter', 'Occupé')"
          :class="{ 'active': filterStatus === 'Occupé' }"
        >
          Occupé
        </button>
        <button
          class="text-success"
          @click="filterStatus = 'Libre'; $emit('filter', 'libre')"
          :class="{ 'active': filterStatus === 'Libre' }"
        >
        Non Occupé
        </button>
        <button
          class="text-muted"
          @click="filterStatus = 'Undefined'; $emit('filter', 'Undefined')"
          :class="{ 'active': filterStatus === 'Undefined' }"
        >
          Undefined
        </button>
        <button
        class="text-muted"
        @click="resetFilter"
      >
        X
      </button>
      </div>
      <div class="input-group">
        <input type="text" class="form-control" placeholder="Recherche par nom du piece" v-model="searchTerm">
        <div class="input-group-append">
          <button class="search" type="button" @click="onSearch">
            <i class="fas fa-search"></i>
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import '@fortawesome/fontawesome-free/css/all.min.css';
  export default {
  data() {
    return {
      filterStatus: '',
      searchTerm: '',
    };
  },
  methods: {
    onSearch() {
        this.$emit('search', this.searchTerm, this.filterStatus);
    },
    resetFilter() {
      this.filterStatus = ''; 
      this.$emit('filter', ''); 
    }
  }
};
</script>

  <style scoped>
  .input-group_mb-3{
    background-image: url('../../public/spinalcom_logo.png');
    background-size: 90%; 
    background-position: center;
    background-repeat: no-repeat; 
    position: absolute;
    top: 25% ;
    margin-left: 2%;
    padding: 50px;
    background-color: #f0f0f0;
    backdrop-filter: blur(10px);
    border: 1px solid #ca8a03;
    box-shadow: 0 0 40px rgba(8,7,16,0.6);
    border-radius: 10%;
    width: auto;
    height: 500px ;
  }
  
  .search {
    background-color: transparent ;
    border: none;
    position: absolute;
    top: 22% ;
    right: 9%;
    color: #ca8a03 ;
  }
  .text-success {
color: #000000;
background-color: #ffffff;
padding: 4px 12px;
border-radius: 10px;
margin-left: 10px;
font-weight: bold;
border: solid 1px;
}
.text-success:active{
color: #fff;
background-color: #4CAF50;
padding: 4px 12px;
border-radius: 20px;
margin-left: 10px;
font-weight: bold;
}

.text-danger{
color: #000000;
background-color: #ffffff;
padding: 4px 12px;
border-radius: 10px;
margin-left: 10px;
font-weight: bold;
border: solid 1px;
}
.text-danger:active{
color: #fff;
background-color: #F44336;
padding: 4px 12px;
border-radius: 20px;
margin-left: 10px;
font-weight: bold;
}

.text-muted {
color: #000000;
background-color: #ffffff;
padding: 4px 12px;
border-radius: 10px;
margin-left: 10px;
font-weight: bold;
border: solid 1px;
}

.text-muted:active{
color: #fff;
background-color: #ca8a03;
padding: 4px 12px;
border-radius: 20px;
margin-left: 10px;
font-weight: bold;
}
.form-control{
    height: 30px;
    width:80% ;
    border: solid 2px #ca8a03;
    border-radius: 15px;
    margin-top: 15%;
    margin-left: 10%;
    text-align: center;
    font-size: large;
}
</style>