<script>
  import BackgroundBlock from "../components/background.svelte";
  import TransitionWrapper from "../components/TransitionWrapper.svelte";
  import { onMount } from "svelte";
  import jq from "jquery";
  $: curTime = "";
  var date = new Date();
  var changedDate = new Date();
  $: curDay = date.getDate();
  $: curMonth = date.getMonth();
  var curYear = date.getFullYear();
  var div;
  var logged = false;
  onMount(async () => {
    window.jq = jq;
    jq("#btn").click(function() {
      var curDate = document.getElementById("curDay").value;
      var formattedDate = curYear + curDate.split('.')[1] + curDate.split('.')[0];
      console.log(formattedDate);
      var eName = document.querySelector("#event-name").value;
      var durationOfEvent = document.getElementById("duration").value;
      var data = {
        name: eName,
        date: formattedDate,
        time: curTime,
        duration: durationOfEvent,
        type: "save_event"
      };
      console.log(data);
      jq.ajax({
        type: "POST",
        headers: { Cookie: document.cookie },
        xhrFields: { withCredentials: true },
        crossDomain: true,
        url: "http://localhost:5000",
        data: JSON.stringify(data)
      });
    });
  jq.ajax({
    type: "POST",
    data: JSON.stringify({type: "get_events"}),
    headers: { Cookie: document.cookie },
    xhrFields: { withCredentials: true },
    crossDomain: true,
    url: "http://localhost:5000"
  }).done(function(res) {console.log(res);
        for (var i = 0; i < res.length; i++) {
            var eventDate = /^\d{4}(?<month>\d{2})(?<day>\d{2})$/.exec(res[i].date).groups;
            var formattedEventDate = eventDate.day + '.' + eventDate.month;
            console.log(formattedEventDate);
            var dateOfEvent = document.getElementById('column_'+formattedEventDate);///////
            var timeString = res[i].time.split(':');
            var timeOffset = parseInt(timeString[0])*60 + parseInt(timeString[1]); 
            createElem(dateOfEvent, timeOffset, res[i].name, res[i].id, res[i].duration);
        }
    });
  });

  function createElem(e, y, name, id, duration) {
    div = document.createElement("div");
    e.appendChild(div);
    div.innerHTML = name;
    div.id = id;
    div.classList.add("some-event");
    curTime =
      Math.floor(y / 60)
        .toString()
        .padStart(2, "0") +
      ":" +
      (y % 60).toString().padStart(2, "0");
    div.style.marginTop = y + "px";
    div.style.height = duration + "px";
    console.log(y);
  }

  function someEvent(e) {
    var wrapper = document.getElementById("event-wrapper");
    wrapper.classList.remove("invisible");
    wrapper.classList.add("visible");
    var curColumnId = e.target.id;
    var curHeaderCellId = "header_" + curColumnId.split("_")[1];
    var curHeaderCell = document.getElementById(curHeaderCellId);
    document.getElementById("curDay").value = curHeaderCell.textContent;
    var y = e.offsetTop == undefined ? e.layerY : e.offsetTop;
    createElem(e.target, y, "something", "created-event-id", 24);
  }

  function saveEvent() {
    var wrapper = document.getElementById("event-wrapper");
    wrapper.classList.remove("visible");
    wrapper.classList.add("invisible");
    var getNum = document.getElementById("duration").value;
    div.style.height = getNum + "px";
    div.innerHTML = document.querySelector("#event-name").value;
    div.id = "saved-event-id";
  }

  function getDay(i) {
    date = new Date();
    date.setDate(date.getDate() + i);
    var formattedDay = date
      .getDate()
      .toString()
      .padStart(2, "0");
    var formattedMonth = (date.getMonth() + 1).toString().padStart(2, "0");
    return formattedDay + "." + formattedMonth;
  }

  function cancelEvent() {
    document.querySelector("#created-event-id").remove();
    var wrapper = document.getElementById("event-wrapper");
    wrapper.classList.remove("visible");
    wrapper.classList.add("invisible");
  }

  onMount(() => {
    console.log("logged");
    if (document.cookie != "") {
      logged = true;
    }
    if (logged == false) {
      document.querySelector("#columns").style.pointerEvents = "none";
    }
  });
</script>

<style>
  :global(.visible) {
    z-index: 1000;
  }

  :global(.invisible) {
    z-index: -1000;
  }

  #table-block {
    display: flex;
    height: 100%;
    flex-direction: column;
  }

  #content {
    position: relative;
    width: 97%;
    height: 85%;
    left: 15%;
  }

  #table-header {
    display: flex;
    width: 100%;
  }

  .week-header-day {
    display: flex;
    justify-content: center;
    width: 13%;
    border-left: solid 1px rgba(0, 0, 0, 0.2);
    border-top: solid 1px rgba(0, 0, 0, 0.2);
    border-bottom: solid 1px;
    box-sizing: border-box;
  }

  #times-block {
    width: 100%;
    display: flex;
    position: relative;
    overflow-y: scroll;
    left: -55px;
  }

  #times-block::-webkit-scrollbar {
    width: 0px;
  }

  #timeRuler-column {
    width: 60px;
  }

  .timeRuler-column-cell {
    height: 60px;
  }

  #columns {
    position: absolute;
    display: flex;
    width: 100%;
    left: 55px;
    z-index: 100;
  }

  .column-wrap {
    text-align: center;
    height: 1440px;
    width: 13%;
    box-sizing: border-box;
    border-left: solid 1px;
    border-color: rgba(0, 0, 0, 0.2);
  }

  #rows {
    width: 100%;
    position: relative;
    display: block;
  }

  .row {
    border-bottom: solid 1px;
    border-color: rgba(0, 0, 0, 0.08);
    height: 59px;
  }

  :global(.some-event) {
    position: absolute;
    width: 13%;
    background-color: rgba(255, 0, 0, 0.3);
    z-index: -10;
  }

  #event-wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
  }

  #event-info-block {
    z-index: 2000;
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 400px;
    height: 230px;
    box-shadow: 0px 0px 5px 5px rgba(0, 0, 255, 0.103);
    border-radius: 20px;
    background-color: rgba(255, 255, 255, 0.75);
    left: 50%;
    top: 35%;
    margin-right: -50%;
    transform: translate(-50%, -50%);
  }

  #event-form {
    display: flex;
    align-items: center;
    flex-direction: column;
  }

  #btn {
    width: 80px;
    height: 30px;
    background-color: rgba(127, 255, 212, 0.8);
    border-radius: 5px;
    margin-top: 3%;
  }

  #cross {
    position: absolute;
    cursor: pointer;
    top: 3%;
    right: 3%;
  }

  #cancel-wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: 1000;
  }

  .form-input {
    margin: 5px 0px;
  }
</style>

<BackgroundBlock />

<div id="event-wrapper" class="invisible">
  <div id="cancel-wrapper" on:click={cancelEvent} />
  <div id="event-info-block">
    <form id="event-form" method="POST" on:submit|preventDefault="{saveEvent}">
      <span id="cross" class="form-input" on:click={cancelEvent}>&#10060;</span>
      Выбранное время: {curTime}
      <input
        id="event-name"
        class="form-input"
        name="event-name"
        placeholder="Название события"
        value="" 
        required />
      <input
        id="duration"
        class="form-input"
        name="duration"
        placeholder="Длительность (в мин)"
        value 
        required />
      <input
        id="btn"
        class="form-input"
        type="submit"
        value="Ok" />
      <input id="curDay" type="hidden" name="curDay" value="" />
    </form>
  </div>
</div>

<div id="content">
  <div id="table-block">
    <div id="table-header">
      {#each Array(7) as _, i}
        <div id="header_{getDay(i)}" class="week-header-day">{getDay(i)}</div>
      {/each}
    </div>
    <div id="times-block">
      <div id="timeRuler-column">
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">00:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">01:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">02:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">03:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">04:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">05:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">06:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">07:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">08:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">09:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">10:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">11:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">12:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">13:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">14:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">15:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">16:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">17:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">18:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">19:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">20:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">21:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">22:00</div>
        </div>
        <div class="timeRuler-column-cell">
          <div class="timeRuler-cell-time">23:00</div>
        </div>
      </div>
      <div id="rows">
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
        <div class="row" />
      </div>
      <div id="columns" on:click={someEvent}>
        {#each Array(7) as _, i}
          <div id='column_{getDay(i)}' class="column-wrap">
          </div>
        {/each}
      </div>
    </div>
  </div>
</div>
