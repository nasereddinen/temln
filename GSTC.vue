<template>
    <div class="wrapper">
      <p>
        Timeline test 2
      </p>
      <select v-model="selectedDate">
        <option v-for="date in dateoption" :key="date" :value="date">
          {{ date }}
        </option>
        </select>
    <button @click="selectInitailDate">Go to selected date</button>
      <div id="visualization"></div>
      <div id="calendar">
      </div>
      <button @click="goToNextDay">Move to Next Day</button>
    </div>
  </template>
  
  <script>
  import { Timeline } from 'vis-timeline/standalone';
  import axios from 'axios';
  
  export default {
    name: 'GSTC',
    components: {},
    data() {
      return {
        currentDate: new Date(),
        initialeDate : new Date(),
        selectedDate: "2023-06-01",
        dateoption :[
            '2023-06-01',
            '2023-06-02',
            '2023-06-03',
        ],

        
        items: [
          {
            id: 1,
            group: 1,
            content: 'item 1',
            start: new Date(new Date().getTime() - 10000000),
            end: new Date(new Date().getTime() + 20000000),
          },
          {
            id: 2,
            group: 0,
            content: 'item 2',
            start: new Date(new Date().getTime() + 10000000),
            end: new Date(new Date().getTime() + 20000000),
          },
        ],
        groups: [
          {
            id: 0,
            content: 'Tech 1',
            className: 'vis-h0',
          },
          {
            id: 1,
            content: 'Tech 2',
            className: 'vis-h0',
          },
        ],
        // timeline options
        options: {
          orientation: 'top',
          itemsAlwaysDraggable: true,
          timeAxis: { 
            scale: 'hour', 
            step: 1 ,
            type: 'range',
            //how to limit the hours to 8am to 5p

            
            
             },
             //zooming on the hours form 8am to 5pm
             margin: {
                item: {
                    horizontal: 10,
                    vertical: 0,
                    
                }
              },
              //zooming on the hours form 8am to 5pm

        format: {
                  minorLabels: {
                    millisecond:'SSS',
                    second:     's',
                    minute:     'HH:mm',
                    hour:       'HH:mm',
                    weekday:    'ddd D',
                    day:        'D',
                    week:       'w',
                    month:      'MMM',
                    year:       'YYYY'
                  },
                  majorLabels: {
                    millisecond:'HH:mm:ss',
                    second:     'D MMMM HH:mm',
                    minute:     'ddd D MMMM',
                    hour:       'ddd D MMMM',
                    weekday:    'MMMM YYYY',
                    day:        'MMMM YYYY',
                    week:       'MMMM YYYY',
                    month:      'YYYY',
                    year:       ''
                  }
},


        stack: false,
             
          zoomMax: 3600000 * 18, //3600000 zoom hour
          //1500000000 zoom day
          //150000000 zoom hour
          minHeight: '700px',
          start: new Date(new Date().getTime() - 100000000),
          end: new Date(new Date().getTime() + 100000000),
          showCurrentTime: true,
          editable: {
            add: true, // add new items by double tapping
            updateTime: true, // drag items horizontally
            updateGroup: true, // drag items from one group to another
            remove: true, // delete an item by tapping the delete button top right
            overrideItems: true,
            limitDragging: true,
            //dragAxis: 'x',
          },

          onUpdate: function (item, callback) {
            item.content = prompt('Edit items text:', item.content);
            if (item.content != null) {
              callback(item); // send back adjusted item
            } else {
              callback(null); // cancel updating the item
            }
          },
          onMove: function (item, callback) {
            if (item.content != null) {
            } else {
              callback(null); // cancel updating the item
            }
          },
        },
        dataLoaded: null,
        timeline: null,
      };
    },
    async mounted() {
      await this.dataLoaded;
      
      // Create a Timeline
      this.timeline = new Timeline(document.getElementById('visualization'));
      this.timeline.setOptions(this.options);
      this.timeline.setGroups(this.groups);
      this.timeline.setItems(this.items);
      console.log(this.options['start']);
     


    },
    methods: {
      goToNextDay() {
        const nextDay = new Date(this.currentDate);
        nextDay.setDate(nextDay.getDate() + 1);
        this.currentDate = nextDay.toLocaleDateString();
      },

      selectInitailDate(){
        const selectedDate = new Date(this.selectedDate);
        const startDate = new Date(selectedDate);
        startDate.setHours(0, 0, 0, 0); // Set time to the beginning of the day
        const endDate = new Date(selectedDate);
        endDate.setHours(23, 59, 59, 999); // Set time to the end of the day

        this.options.start = startDate;
        this.options.end = endDate;
        

        // Update the timeline

        this.updateTimeline();
      

      },

      updateTimeline() {
        //destroy timeline
        this.timeline.destroy();
        // Create a Timeline
        this.timeline = new Timeline(document.getElementById('visualization'));
        this.timeline.setOptions(this.options);
        this.timeline.setGroups(this.groups);
        this.timeline.setItems(this.items);
      },
      
    },
  };
  </script>
  
  <style>
    .vis-time-axis .vis-grid.vis-odd {
      background: #f5f5f5;
    }
    .vis-time-axis .vis-grid.vis-saturday,
    .vis-time-axis .vis-grid.vis-sunday {
      background: gray;
    }
    .vis-time-axis .vis-text.vis-saturday,
    .vis-time-axis .vis-text.vis-sunday {
      color: white;
    }
    .vis-item {
      background-color: #d6e6ff;
      border-color: #1371fe;
      color: #0155d3;
      border-left-width: 3px;
      border-left-style: solid;
      font-weight: 500;
      opacity: 0.8;
      font-size: 13px;
      height: 55px;
    }
    .vis-h0 .vis-h01 .vis-h15 .vis-h16 {
      color: blue !important;
      height: 100px;
      text-align: center;
    }
  </style>
  