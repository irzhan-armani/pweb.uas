<template>
  <section class="hero is-small">
    <div class="hero-body">
      <p class="title">
        Barang
      </p>
      <p class="subtitle">
        Menampilkan barangg
      </p>
    </div>
  </section>
  <section class="content">
    <div class="container">
      <button class="button is-link" @click="showAdd">
        <span class="icon is-small">
                    <i class="fas fa-plus"></i>
                  </span>
        <span>Data baru</span>
      </button>
      <table class="table">
        <thead>
        <tr>
          <th>#</th>
          <th>ID_Barang</th>
          <th>Nama_Barang</th>
          <th>harga</th>
          <th>kategori</th>
          <th>brand</th>
          <th>tahun</th>
          <th>#</th>
        </tr>
        </thead>
        <tbody>
        <tr v-if="data.length" v-for="(value, index) in data">
          <td>{{index + 1}}</td>
          <td>{{value.ID_Barang}}</td>
          <td>{{value.Nama_barang}}</td>
          <td>{{value.harga}}</td>
          <td>{{value.kategori}}</td>
          <td>{{value.brand}}</td>
          <td>{{value.tahun}}</td>
          <td>
            <p class="control">
              <RouterLink class="button is-success" :to="{ name: 'barang-detail', params: { ID_Barang:  value.ID_Barang}}">
                  <span class="icon is-small">
                    <i class="fas fa-arrow-right"></i>
                  </span>
                <span>Detail</span>
              </RouterLink>
            </p>
            <div class="field has-addons">
              <p class="control">
                <button class="button is-warning" @click="showUpdate(index)">
                  <span class="icon is-small">
                    <i class="fas fa-pencil"></i>
                  </span>
                  <span>Edit</span>
                </button>
              </p>
              <p class="control">
                <button class="button is-danger" @click="showDelete(index)">
                  <span class="icon is-small">
                    <i class="fas fa-trash"></i>
                  </span>
                  <span>Delete</span>
                </button>
              </p>
            </div>
          </td>
        </tr>
        <tr v-else>
          <td colspan="9">
            <div class="notification is-warning">
              Tidak ada data
            </div>
          </td>
        </tr>
        </tbody>
      </table>
    </div>
    <div class="has-text-centered" v-if="isLoading">
      <i class="fa-solid fa-spinner fa-pulse"></i>
    </div>
  </section>
  <div class="modal" id="modal-delete">
    <div class="modal-background"></div>
    <div class="modal-card">
      <header class="modal-card-head">
        <p class="modal-card-title">Delete</p>
      </header>
      <section class="modal-card-body">
        <div v-if="selectedIndex > -1">
          <ul>
            <li>ID_Barang <strong>{{data[selectedIndex].ID_Barang}}</strong></li>
            <li>Nama_Barang <strong>{{data[selectedIndex].Nama_barang}}</strong></li>
            <li>harga <strong>{{data[selectedIndex].harga}}</strong></li>
          </ul>
        </div>
      </section>
      <footer class="modal-card-foot">
        <button class="button is-danger" @click="remove">Delete</button>
        <button class="button" @click="closeModal('modal-delete')">Close</button>
      </footer>
    </div>
  </div>
  <div class="modal" id="modal-update">
    <div class="modal-background"></div>
    <div class="modal-card">
      <header class="modal-card-head">
        <p class="modal-card-title">Update</p>
      </header>
      <section class="modal-card-body">
        <div v-if="selectedIndex > -1">
          <form @submit.prevent="update">
            <div class="field">
              <label class="label" for="Nama_barang_update">Nama_barang</label>
              <div class="control">
                <input class="input" type="text" id="Nama_barang_update" placeholder="Nama_barang" required v-model="formEdit.Nama_barang">
              </div>
            </div>
            <div class="field">
              <label class="label" for="harga_update">harga</label>
              <div class="control">
                <input class="input" type="number" id="harga_update" placeholder="harga" required v-model="formEdit.harga">
              </div>
            </div>

            <div class="field">
              <label class="label" for="kategori_update">kategori</label>
              <div class="field has-addons">
                <p class="control is-expanded">
                  <input class="input" type="text" id="kategori_update" placeholder="kategori" required v-model="formEdit.kategori" min="1" max="10000">
                </p>
                <p class="control">
                  <a class="button is-static">
                    item
                  </a>
                </p>
              </div>
            </div>
            <div class="field">
              <label class="label" for="brand_update">brand</label>
              <div class="control">
                <input class="input" type="text" id="brand_update" placeholder="brand" required v-model="formEdit.brand">
              </div>
            </div>
            <div class="field">
              <label class="label" for="tahun_update">tahun</label>
              <div class="control">
                <textarea id="tahun_update" class="textarea" placeholder="tahun" v-model="formEdit.tahun"></textarea>
              </div>
            </div>
          </form>
        </div>
      </section>
      <footer class="modal-card-foot">
        <button class="button is-success" @click="update">Update</button>
        <button class="button" @click="closeModal('modal-update')">Close</button>
      </footer>
    </div>
  </div>
  <div class="modal" id="modal-add">
    <div class="modal-background"></div>
    <div class="modal-card">
      <header class="modal-card-head">
        <p class="modal-card-title">Add new</p>
      </header>
      <section class="modal-card-body">
        <form @submit.prevent="addNew">
          <div class="field">
            <label class="label" for="ID_Barang_insert">ID_Barang</label>
            <div class="control">
              <input class="input" type="text" id="ID_Barang_insert" placeholder="ID_Barang" required v-model="formAdd.ID_Barang">
            </div>
          </div>
          <div class="field">
            <label class="label" for="Nama_barang_insert">Nama_barang</label>
            <div class="control">
              <input class="input" type="text" id="Nama_barang_insert" placeholder="Nama_barang" required v-model="formAdd.Nama_barang">
            </div>
          </div>
          <div class="field">
            <label class="label" for="harga_insert">harga</label>
            <div class="control">
              <input class="input" type="number" id="harga_insert" placeholder="harga" required v-model="formAdd.harga">
            </div>
          </div>
          <div class="field">
            <label class="label" for="kategori_insert">kategori</label>
            <div class="field has-addons">
              <p class="control is-expanded">
                <input class="input" type="text" id="kategori_insert" placeholder="kategori" required v-model="formAdd.kategori">
              </p>
              <p class="control">
                <a class="button is-static">
                  item
                </a>
              </p>
            </div>
          </div>
          <div class="field">
            <label class="label" for="brand_insert">brand</label>
            <div class="control">
              <input class="input" type="text" id="brand_insert" placeholder="brand" required v-model="formAdd.brand">
            </div>
          </div>
          <div class="field">
            <label class="label" for="tahun_insert">tahun</label>
            <div class="control">
              <textarea id="tahun_insert" class="textarea" placeholder="tahun" v-model="formAdd.tahun"></textarea>
            </div>
          </div>
        </form>
      </section>
      <footer class="modal-card-foot">
        <button class="button is-success" @click="addNew">Save</button>
        <button class="button" @click="closeModal('modal-add')">Close</button>
      </footer>
    </div>
  </div>
</template>

<script>
import {nextTick} from "vue";

export default {
  name: "barang",
  data(){
    return{
      data: [],
      selectedIndex: -1,
      isLoading: false,
      formAdd: {
        ID_Barang: '',
        Nama_barang: '',
        harga: '',
        kategori: '',
        brand: '',
        tahun: '',
      },
      formEdit: {
        ID_Barang: '',
        Nama_barang: '',
        harga: '',
        kategori: '',
        brand: '',
        tahun: '',
      }
    }
  },
  methods:{
    load(){
      this.isLoading = true;
      //const baseurl = import.meta.env.VITE_URL_ENDPOINT_2;
      fetch(`https://martinwebszz.000webhostapp.com/barang/all.php`,{
        method: 'GET',
      })
          .then(response => {
            if(response.ok){
              return response.json();
            }
          })
          .then(json => {
            this.data = json.data;
          })
          .catch(()=>{
            alert('Terjadi error')
          })
          .finally(()=>{
            this.isLoading = false;
          })
    },

    remove(){
      this.closeModal('modal-delete');
      if(this.selectedIndex >= 0){
       // const baseurl = import.meta.env.VITE_URL_ENDPOINT_2;
        const selectedData = this.data[this.selectedIndex];

        const payload = new URLSearchParams({
          'ID_Barang': selectedData.ID_Barang
        });

        fetch(`https://martinwebszz.000webhostapp.com/barang/delete.php`,{
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
          },
          body: payload.toString(),
        })
            .then(response => {
              if(response.ok){
                this.data.splice(this.selectedIndex, 1);
              }
              return response.json();
            })
            .then(json =>{
              if(!json.status){
                alert(json.error);
              }
            })
            .catch(()=>{
              alert('Terjadi error')
            })
      }
      this.selectedIndex = -1;
    },
    update(){
      this.closeModal('modal-update');
      if(this.selectedIndex >= 0){
        //const baseurl = import.meta.env.VITE_URL_ENDPOINT_2;
        const payload = new URLSearchParams({
          ID_Barang: this.formEdit.ID_Barang,
          Nama_barang: this.formEdit.Nama_barang,
          harga: this.formEdit.harga,
          kategori: this.formEdit.kategori,
          brand: this.formEdit.brand,
          tahun: this.formEdit.tahun,
        });

        fetch(`https://martinwebszz.000webhostapp.com/barang/update.php`,{
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
          },
          body: payload.toString(),
        })
            .then(response => {
              return response.json()
            })
            .then(json =>{
              if(!json.status){
                alert(json.error);
              }else{
                /*
                Update data pada kolom yg diupdate
                 */
                this.data[this.selectedIndex] = json.data;
              }
            })
            .catch( (e) =>{
              alert('Terjadi error'+e.toString())
            })
            .finally(()=>{
              this.selectedIndex = -1;
            })
      }
    },
    showDelete(index){
      this.showModal('modal-delete');
      this.selectedIndex = index;
    },
    showUpdate(index){
      this.showModal('modal-update');
      this.selectedIndex = index;
      const selectedData = this.data[index];
      this.formEdit = {
        ID_Barang: selectedData.ID_Barang,
        Nama_barang: selectedData.Nama_barang,
        harga: selectedData.harga,
        kategori: selectedData.kategori,
        brand: selectedData.brand,
        tahun: selectedData.tahun,
      }
      nextTick(()=>{
        document.getElementById('Nama_barang_update').focus();
      })
    },
    showAdd(){
      this.resetFormAdd();
      this.showModal('modal-add');
      nextTick(()=>{
        document.getElementById('ID_Barang_insert').focus();
      })
    },
    addNew(){
      this.closeModal('modal-add');
      //const baseurl = import.meta.env.VITE_URL_ENDPOINT_2;
      const payload = new URLSearchParams({
        ID_Barang: this.formAdd.ID_Barang,
        Nama_barang: this.formAdd.Nama_barang,
        harga: this.formAdd.harga,
        kategori: this.formAdd.kategori,
        brand: this.formAdd.brand,
        tahun: this.formAdd.tahun,
      });

      fetch(`https://martinwebszz.000webhostapp.com/barang/Create.php`,{
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: payload.toString(),
      })
          .then(response => {
            return response.json()
          })
          .then(json =>{
            if(!json.status){
              alert(json.error);
            }else{
              this.data.push(json.data);
            }
          })
          .catch( (e) =>{
            alert('Terjadi error'+e.toString())
          })
          .finally(()=>{
            this.formAdd.nama = '';
          })
    },
    showModal(id){
      document.getElementById(id).classList.add('is-active');
    },
    closeModal(id){
      document.getElementById(id).classList.remove('is-active');
    },
    resetFormAdd(){
      this.formAdd = {
        ID_Barang: '',
        Nama_barang: '',
        harga: '',
        kategori: '',
        brand: '',
        tahun: '',
      }
    }
  },
  mounted() {
    this.load();
  }
}
</script>

<style scoped>

</style>