<template>
  <div>
    <div class="row">
      <div class="col-lg-2">
        <span v-if="user">
          <Navbar v-if="user.user_metadata.tipe_user == 'admin'" />
          <Apoteker v-if="user.user_metadata.tipe_user == 'apoteker'" />
          <Kasir v-if="user.user_metadata.tipe_user == 'kasir'" />
        </span>
      </div>
      <div class="col-lg-10 px-5 py-4">
        <div class="card">
          <div class="row">
            <h2 class="col-lg-3">Transaksi</h2>
            <div class="col-lg-9">
              <nuxt-link to="/riwayattransaksi">
                <button type="button" class="btn btn-primary me-5">
                  Riwayat Transaksi
                </button>
              </nuxt-link>
            </div>
          </div>
          <div class="my-3">
            <div class="mb-3">
              <form @submit.prevent="tambahTransaksi">
                <div class="row">
                  <div class="col-lg-6">
                    <select
                      class="form-select mb-3"
                      aria-label="Default select example"
                    >
                      <option selected>Tipe Resep</option>
                      <option value="resep">Resep</option>
                      <option value="nonresep">Non resep</option>
                    </select>
                    <input
                      type="text"
                      class="form-control mb-3"
                      placeholder="No Resep"
                    />
                    <input
                      type="date"
                      class="form-control mb-3"
                      placeholder="Tgl Resep"
                    />
                    <input
                      type="text"
                      class="form-control mb-3"
                      placeholder="Nama Pasien"
                    />
                    <button type="submit" class="btn btn-primary">
                      Submit
                    </button>
                  </div>
                  <div class="col-lg-6">
                    <input
                      type="text"
                      class="form-control mb-3"
                      placeholder="Nama Dokter"
                    />
                    <select v-model="harga" class="form-select mb-3">
                      <option selected value="">Nama Obat</option>
                      <option v-for="(obat, i) in Obat" :value="obat.Harga">
                        {{ obat.Nama_Obat }}
                      </option>
                    </select>
                    <input
                      v-model="harga"
                      type="number"
                      class="form-control mb-3"
                      placeholder="Harga"
                      readonly
                    />
                    <input
                      v-model="Quantity"
                      type="number"
                      class="form-control mb-3"
                      placeholder="Jumlah"
                    />

                    Rp. {{ Total_Bayar }}
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const user = useSupabaseUser();
const supabase = useSupabaseClient();

const Quantity = ref();
const harga = ref();
const Total_Bayar = computed(() => Quantity.value * harga.value);
const Obat = ref([]);

const getObat = async () => {
  const { data, error } = await supabase.from("Tbl_Obat").select(`*`);
  if (data) Obat.value = data;
};

const tambahTransaksi = async () => {
  const { data } = await supabase.from("Tbl_Transaksi").insert({
    Total_Bayar: Total_Bayar.value,
    Quantity: Quantity.value,
  });
  if (data) {
    navigateTo("/riwayattransaksi");
  }
};

onMounted(() => {
  getObat();
});
</script>
