
<script>
import axios from 'axios'
import { Line } from 'vue-chartjs'

export default {
  extends: Line,
  data () {
    return {
      timeStamp: [],
      powerUsage: []
    }
  },
  mounted () {
    // this.threeNPlusOne(1e2)
    this.initDataRetrieving()
  },
  methods: {
    threeNPlusOne (n = 1) {
      const cong = (x)=>{
        if(x%2 === 0) return x/2
        else return x*3+1
      }

      const xplane = []
      const yplane = []
      for(let i=1; i<=n; i++) {
        // if(i%100 !==0) { continue }
        let count = 0;
        let curr = i;
        while(curr !== 1){
          curr = cong(curr)
          count++;
        }
        xplane.push(i)
        yplane.push(count)
        console.log(`${i}: ${count}`)
      }
      this.timeStamp = xplane
      this.powerUsage = yplane
      this.chartPlot()

    },
    initDataRetrieving () {
      this.getData()
      const PowerApiInterval = setInterval(()=> {
        this.getData()
      }, 1000 * 10)
      setTimeout(() => {
        clearInterval(PowerApiInterval)
      }, 1000 * 60 * 10)
    },
    getFilteredData (data = 0) {
      if(data === 0) { return null
      }
      const dateObj = new Date(data)
      const sec = String(dateObj.getSeconds()).padStart(2, '0')
      const min = String(dateObj.getMinutes()).padStart(2, '0')
      const hour = String(dateObj.getHours()).padStart(2, '0')

      const day = String(dateObj.getDate()).padStart(2, '0');
      const month = String(dateObj.getMonth()+1).padStart(2, '0');
      
      // const year = dateObj.getFullYear();
      const output = `${hour}:${min}:${sec} ${day}/${month}`
      return output
    },
    getData() {
      console.log('mounted')
      const options = {
        method: 'GET',
        url: 'https://smartenergyconservation-default-rtdb.asia-southeast1.firebasedatabase.app/UsersData/node1.json',
      }
      axios(options).then(res => {
        
        let temp = 0
        const TOTAL_TIME_RANGE = 19*60*60/10
        // for(let i = 0; i < res.data.Power.length; i++) {
        //   temp += res.data.Power[i]
        //   if(i % 60 === 59) {
        //     this.powerUsage.push(temp/60*235)
        //     temp = 0
        //   }
        // }
        // for(let i = 0; i < res.data.Time.length; i++) {
        //   temp = res.data.Time[i]
        //   if(i % 60 === 59) {
        //     this.timeStamp.push(temp)
        //   }
        // }
        

        this.timeStamp = res.data.Time.slice(res.data.Time.length - TOTAL_TIME_RANGE, res.data.Time.length)
        
        
        this.powerUsage = res.data.Power.slice(res.data.Power.length - TOTAL_TIME_RANGE, res.data.Power.length)
        temp = 0
        this.powerUsage = this.powerUsage.map((amp, i) => {
          temp = amp*1
          return temp
        })

        this.timeStamp = this.timeStamp.map(value => this.getFilteredData(value*1000 - 1000*3600*5.5))
        console.log(this.timeStamp, this.powerUsage)
        this.chartPlot()

      })
    },
    chartPlot() {
      const chartData = {
        labels: this.timeStamp,
        datasets: [
          {
            label: 'amp',
            data: this.powerUsage,
            fill: false,
            borderColor: '#2554FF',
            backgroundColor: '#2554FF',
            borderWidth: 0.5
          }
        ]
      }
      const options = {
        scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            },
            gridLines: {
              display: true,
            }
          }],
          xAxes: [ {
            gridLines: {
              display: false
            }
          }]
        },
        legend: {
          display: true
        },
        animation: {
          duration: 0
        },
        zoom: {
          enabled: true,
          drag: false,
          mode: 'x'
        },
        responsive: true,
        maintainAspectRatio: false
      }
      this.renderChart(chartData, options)
    }
  }
}
</script>
