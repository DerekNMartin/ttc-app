<template>
  <div class="hello">
    <h1>King St. West At Jefferson Ave.</h1>
    <h2>Last Updated: {{ getCurrentTime() }}</h2>
    <template v-if="routes.length > 0">
      <h2>{{ routes[0].routeTitle }}</h2>
      Next Arrival in <span class="highlight">{{ routes[0].times[0] }} minutes</span> and {{ routes[0].times[1] }} minutes
      <h2>{{ routes[1].routeTitle }}</h2>
      Next Arrival in <span class="highlight">{{ routes[1].times[0] }} minutes</span> and {{ routes[1].times[1] }} minutes  
    </template>
    <template v-else>
      No Data
    </template>
    </div>
</template>

<script>
const TTC_ROUTE = 'http://webservices.nextbus.com/service/publicXMLFeed?command=predictions&a=ttc&stopId=4175'
const axios = require('axios')
const convert = require('xml-js')
export default {
  name: 'StopTime',
  mounted: function () {
    this.getStopData()
  },
  data () {
    return {
      routes: [],
    }
  },
  methods: {
    convertToJSON(xml) {
      let _result = convert.xml2json(xml, {compact: true, spaces: 4})
      return JSON.parse(_result).body.predictions
    },
    getStopData() {
      this.getCurrentTime()
      let _self = this 
      axios.get(TTC_ROUTE)
      .then(function (response) {
        console.log('Get Stop Data Success!')
        let _stopData = _self.convertToJSON(response.data)
        _self.setRoutes(_stopData)
      })
      .catch(function (error) {
        console.log(error)
      });
    },
    setRoutes(stopData) {
      let _routes = stopData
      _routes.forEach((route) => {
        let _details = route._attributes
        let _times = this.getNextTimes(route.direction.prediction)
        this.routes.push({
              routeTag: _details.routeTag,
              routeTitle: _details.routeTitle,
              stopTitle: _details.stopTitle,
              times: _times
        })
      })
      console.log(this.routes)
    },
    getNextTimes(times) {
      let _nextTimes = []
      times.forEach((time) => {
        _nextTimes.push(time._attributes.minutes)
      })
      return _nextTimes
    },
    getCurrentTime() {
      let _now = new Date(Date.now())
      let _minute = _now.getMinutes()
      let _hour = _now.getHours()
      let _amPM = (_hour > 11) ? 'pm' : 'am'
      if (_hour > 12) {
        _hour -= 12
      } else if (_hour == 0) {
        _hour = '12'
      }
      if (_minute < 10) {
        _minute = `0${_minute}`
      }
      let _time = `${_hour}:${_minute}${_amPM}`
      return _time
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.highlight {
  color: red
}
</style>
