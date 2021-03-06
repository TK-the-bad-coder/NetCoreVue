<template>
  <div class="section">
    <div class="container">
      <h1 class="title is-spaced">Session Synopsis</h1>

      <b-table :data="sessions"
               :striped="true"
               paginated
               :per-page="perPage"
               :default-sort-direction="defaultSortDirection"
               :selected.sync="selected"
               default-sort="sessionSynopsisName">

        <template slot-scope="props">

          <b-table-column field="sessionSynopsisName"
                          label="Name"
                          sortable>
            {{ props.row.sessionSynopsisName }}
          </b-table-column>

          <b-table-column field="isVisible"
                          label="Visibility">
            <b-icon v-if="props.row.isVisible"
                    pack="fa"
                    icon="check" />
          </b-table-column>

          <b-table-column field="createdBy"
                          label="Created By">
            {{ props.row.createdBy }}
          </b-table-column>

          <b-table-column field="updatedBy"
                          label="Updated By">
            {{ props.row.updatedBy }}
          </b-table-column>

          <b-table-column label="Actions">
            <router-link class="button is-warning"
                         :to="{ name: 'editSynopsis', params: { id: props.row.sessionSynopsisId }}">
              <b-icon pack="fa"
                      icon="pencil" />
              <span>Edit</span>
            </router-link>
            <button class="button is-danger"
                    @click="confirmDelete(props.row.sessionSynopsisId)">
              <b-icon pack="fa"
                      icon="trash-o" />
              <span>Delete</span>
            </button>
          </b-table-column>
        </template>

        <!-- Footer -->
        <template slot="footer">
          <button class="button is-primary"
                  @click="addSynopsis">
            Add Synopsis
          </button>
        </template>

      </b-table>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      sessions: [],
      defaultSortDirection: 'asc',
      perPage: 10,
      selected: null
    }
  },
  async created () {
    try {
      const { redirected, data } = await axios.get('/api/SessionSynopsis')
      if (!redirected) {
        this.sessions = data
      } else {
        this.$router.push({ path: '/Error' })
      }
    } catch (err) {
      console.error(err)
    }
  },
  methods: {
    sessionEdit (id) {
      router.push({ name: 'Edit', params: id })
    },
    confirmDelete (id) {
      this.$dialog.confirm({
        title: 'Deleting synopsis',
        message: 'Are you sure you want to <b>delete</b> this session synopsis? This action cannot be undone.',
        confirmText: 'Delete Synopsis',
        type: 'is-danger',
        hasIcon: true,
        onConfirm: () => {
          axios.delete(`/api/SessionSynopsis/${id}`)
            .then(({ status }) => {
              if (status === 200) {
                return true
              } else {
                throw new Error(response)
              }
            })
            .then(() => axios.get('/api/SessionSynopsis'))
            .then(({ data }) => this.sessions = data)
            .then(() => this.$toast.open('Session synopsis deleted!'))
            .catch(err => console.error(err))
        }
      })
    },
    addSynopsis () {
      this.$router.push('/Sessions/Create')
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
