<template>
  <CRow>
    <CCol col="12" xl="8">
      <CCard>
        <CCardHeader>
          Users
        </CCardHeader>
        <CCardBody>
          <CDataTable
            hover
            striped
            :items="items"
            :fields="fields"
            :items-per-page="5"
            clickable-rows
            :active-page="activePage"
            @row-clicked="rowClicked"
            :pagination="{ doubleArrows: false, align: 'center'}"
            @page-change="pageChange"
          >
            <template #status="data">
              <td>
                <CBadge :color="getBadge(data.item.status)">
                  {{data.item.status == 1 ? 'Active' : 'Inactive'}}
                </CBadge>
              </td>
            </template>
          </CDataTable>
        </CCardBody>
      </CCard>
    </CCol>
  </CRow>
</template>

<script>
import usersData from './UsersData'
import http from 'axios';
export default {
  name: 'Users',
  data () {
    return {
      items: [],
      fields: [
        { key: 'name', label: 'Name', _classes: 'font-weight-bold' },
        { key: 'email' },
        { key: 'type' },
        { key: 'status' }
      ],
      activePage: 1
    }
  },
  watch: {
    $route: {
      immediate: true,
      handler (route) {
        if (route.query && route.query.page) {
          this.activePage = Number(route.query.page)
        }
      }
    }
  },
  methods: {
    getBadge (status) {
      switch (status) {
        case 1: return 'success'
        case 0: return 'secondary'
        default: 'primary'
      }
    },
    rowClicked (item, index) {
      this.$router.push({path: `users/${index + 1}`})
    },
    pageChange (val) {
      this.$router.push({ query: { page: val }})
    },
    getUsers() {
      const token = sessionStorage.getItem("token");
     
      http.get("http://student-api.test/api/users",{ headers: { Authorization: 'Bearer '+token } }).then( response => {
        const result = response.data;
        this.items = result;
        console.log(result);
      }).catch( error => {
        if (error.response && (error.response.status === 404 || error.response.status === 500)){
          console.log(error.response);
        }
      });
    }
  },
  created(){
    this.getUsers();
  }
}
</script>
