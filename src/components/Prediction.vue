<template>
<div id="prediction">
<h1>Prediction for Number of People</h1>
<form>
    <label for="day">Day: </label>
    <select v-model="day">
        <option value=01>1st</option>
        <option value=02>2nd</option>
        <option value=03>3rd</option>
        <option value=04>4th</option>
        <option value=05>5th</option>
        <option value=06>6th</option>
        <option value=07>7th</option>
        <option value=08>8th</option>
        <option value=09>9th</option>
        <option value=10>10th</option>
        <option value=11>11th</option>
        <option value=12>12th</option>
        <option value=13>13th</option>
        <option value=14>14th</option>
        <option value=15>15th</option>
        <option value=16>16th</option>
        <option value=17>17th</option>
        <option value=18>18th</option>
        <option value=19>19th</option>
        <option value=20>20th</option>
        <option value=21>21st</option>
        <option value=22>22nd</option>
        <option value=23>23rd</option>
        <option value=24>24th</option>
        <option value=25>25th</option>
        <option value=26>26th</option>
        <option value=27>27th</option>
        <option value=28>28th</option>
        <option value=29>29th</option>
        <option value=30>30th</option>
        <option value=31>31st</option>
    </select>
    <br>
    <label for="month">Month: </label>
    <select  v-model="month">
        <option value=01>January</option>
        <option value=02>February</option>
        <option value=03>March</option>
        <option value=04>April</option>
        <option value=05>May</option>
        <option value=06>June</option>
        <option value=07>July</option>
        <option value=08>August</option>
        <option value=09>September</option>
        <option value=10>October</option>
        <option value=11>November</option>
        <option value=12>December</option>
    </select>
    <br>
    <label for="year">Year: </label>
    <select v-model="year">
        <option value=2021>2021</option>
        <option value=2022>2022</option>
    </select>
    <br>
    <label for="hour">Time: </label>
    <select v-model="hour">
        <option value="00">00</option>
        <option value="01">01</option>
        <option value="02">02</option>
        <option value="03">03</option>
        <option value="04">04</option>
        <option value="05">05</option>
        <option value="06">06</option>
        <option value="07">07</option>
        <option value="08">08</option>
        <option value="09">09</option>
        <option value="10">10</option>
        <option value="11">11</option>
        <option value="12">12</option>
        <option value="13">13</option>
        <option value="14">14</option>
        <option value="15">15</option>
        <option value="16">16</option>
        <option value="17">17</option>
        <option value="18">18</option>
        <option value="19">19</option>
        <option value="20">20</option>
        <option value="21">21</option>
        <option value="22">22</option>
        <option value="23">23</option>
    </select>
    <label for="minute"></label>
    <select v-model="minute">
        <option value=":00">:00</option>
        <option value=":15">:15</option>
        <option value=":30">:30</option>
        <option value=":45">:45</option>
    </select>
  
</form>
<br>
<button v-on:click="getPredictionCall">Get Prediction</button>
<br>
<div> {{predictionString}} </div>
<!-- CAN BE USED INSTEAD OF {{predictionString}} ABOVE -->
<!--
<button v-on:click="getPredictionAlert">Get Prediction Alert</button>
-->
</div>
</template>

<script>

// Prediction result with popup
function predictionAlert() {
    alert("The predicted number of people on " + data.day + "-" + data.month + "-" + data.year + " at " + data.hour  + data.minute + ": " + predictedValue  )
}

// Variables
const predictionModelURL = 'https://people-model.herokuapp.com/'
// Overwritten on each prediction call
let predictedValue  = null
// Overwritten on each prediction call
let predictionString = ""

// Returned in export default
let data = {
    day:"01",
    month:"01",
    year:"2021",
    hour:"09",
    minute:":00",
    predictedValue :null,
    predictionString:''
}

// Logs the result
function predictionLog() {
    console.log("Prediction: " + predictedValue )
}

// Gets datetime data from user input, formats it, and sends it to the API. Returns the resolved promise
async function getPrediction(day, month, year, hour, minute) {
    let timeString = day + '-' + month + '-' + year + ' ' + hour + minute
    let predictionData = {'data': timeString}

    const predictionResponse = await postData(predictionModelURL, predictionData)
    return predictionResponse
}

// Extracts result from nested JSON object
async function getNumericValueFromResponse(response) {
    predictedValue  = response['results']['results']
    
}

// Local variable has to be used, so this has to exist for compilation
function usePredictionString() {
    console.log(predictionString)
}

// Sends a request with the cleaned data to the API, and result is returned as a promise
async function postData(url = '', data = {}) {
    const response = await fetch(url, {
        method: 'POST',
        mode: 'cors',
        cache: 'no-cache', 
        credentials: 'same-origin', 
        headers: {
            'Content-Type': 'application/json',
        },
        redirect: 'follow', 
        referrerPolicy: 'no-referrer', 
        body: JSON.stringify(data)
    });
    return response.json(); 
}


export default{
    name: "Prediction",
    data(){
        return data
    },
    methods :{
        // Any "get..." function just calls other methods as declared above
        getPredictionCall() {
            getPrediction(this.day, this.month, this.year, this.hour, this.minute)
            .then(getNumericValueFromResponse)
            .then(this.updateData)
            .then(this.getPredictionLog)
        },

        getPredictionLog() {
            predictionLog()
        },
        // Changes variable values in data object in export default block
        updateData() {
            this.data = data
            this.predictedValue = 0
            this.predictedValue = predictedValue 
            this.predictionString = "Predicted number of people on " + data.day + "-" + data.month + "-" + data.year + " at " + data.hour  + data.minute + ": " + predictedValue 
        },

        getPredictionAlert() {
            predictionAlert()
        },

        // Only here because predictionString has to be used
        getUsePredictionString() {
            usePredictionString()
        }
    }
}
</script>