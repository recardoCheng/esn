
<template>
  <v-card>
    <v-card-title>
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>

    <v-data-table
      :headers="headers"
      :items="devices"
      :search="search"
      :items-per-page="40"
      style="margin-left: 8%; margin-right:8%"
    >
    <template v-slot:item.status="{ item }">
      <v-chip :color="getColor(item.status)" dark></v-chip>
    </template>

    </v-data-table>

  </v-card>
</template>
<script>

export default {
  data () {
    return {
      search: '',
      headers: [
        {
          text: '店名',
          align: 'start',
          sortable: true,
          filterable: true,
          class: 'title',
          value: 'name',
          width: '40%',
          fixed: true
        },
        {
          text: '設備Mac',
          align: 'start',
          sortable: false,
          filterable: true,
          class: 'title',
          value: 'mac',
          width: '30%',
          fixed: true
        },
        {
          text: '在線狀態',
          align: 'start',
          sortable: false,
          filterable: true,
          class: 'title',
          value: 'status',
          fixed: true
        }
      ],
      devices: [
      ]
    }
  },

  methods: {
    getColor: function (status) {
      if (status === 0) {
        return 'red'
      } else if (status === 1) {
        return 'green'
      } else {
        return 'orange'
      }
    }
  },

  beforeMount () {
    // var esndevices = this.$data.devices
    var that = this
    var api = 'http://54.68.192.24:18080/users/devicelist'

    this.$axios.get(api)
      .then(function (response) {
        that.$data.devices = []
        for (var k = 0; k < response.data.esn.length; k++) {
          that.$data.devices.push(response.data.esn[k])
        }

        var deviceObj = {}
        for (var i = 0; i < response.data.linkState.length; i++) {
          var linkState = response.data.linkState[i].linkState
          console.log(response.data.linkState[i].mac, linkState)
          deviceObj[response.data.linkState[i].mac] = {
            status: (linkState === 'LinkUp')
          }
        }

        console.log(that.$data.devices)

        for (var j = 0; j < that.$data.devices.length; j++) {
          var macKey = that.$data.devices[j].mac.replace(/:/g, '').toLowerCase()
          if (deviceObj[macKey] !== undefined) {
            console.log(macKey, deviceObj[macKey])
            if (deviceObj[macKey].status) {
              that.$data.devices[j].status = 1
            } else {
              that.$data.devices[j].status = 0
            }
          } else {
            that.$data.devices[j].status = -1
          }
        }
      })
  }
}
</script>
