<script>

  import Sugar from 'sugar-and-spice';
  if (typeof Date.create == "undefined") {
    // sugar was not already extended
    Sugar.extend();
  }  

  import { FlexRow, FlexRowCell } from "braintapper-svelte-flex";
  import { ButtonIcon } from "braintapper-svelte-button";
  import { IconSvg, IconToggleSvg } from "braintapper-svelte-icon";


  export let value = null;
  export let events = [];

  

  import { createEventDispatcher  } from 'svelte';

  let dispatch = createEventDispatcher();


  let weeks = [];



  let currentCalendar = [];
  let currentMonth = null;
  let currentCalendarHeader = null;

  let generate = function(baseDate) {
    var calendarEnd, calendarStart, count, currentWeek, outputWeeks, runningDate;
    outputWeeks = [];
    currentCalendarHeader = baseDate.format("{Month} {yyyy}");
    currentMonth = baseDate.format("{M}");
    calendarStart = baseDate.clone().beginningOfMonth().beginningOfWeek();
    calendarEnd = baseDate.clone().endOfMonth().endOfWeek();
    runningDate = calendarStart.clone();
    currentWeek = [];
    count = 0;
    while (runningDate.isBetween(calendarStart, calendarEnd)) {
      currentWeek.push(runningDate.clone());
      runningDate.addDays(1);
      count++;
      if (count === 7) {
        outputWeeks.push(currentWeek);
        currentWeek = [];
        count = 0;
      }
    }
    return weeks = outputWeeks;
  };

  let displayDate = null;

  let current = function(dateValue) {
    if ((typeof value !== "undefined") && (value !== null)) {
      if (Object.isDate(dateValue)) {
        return displayDate = dateValue;
      } else {
        return displayDate = Date.create(dateValue).reset();
      }
    } else {
      return displayDate = Date.create().reset();
    }
  };

  let next = function() {
    displayDate.addMonths(1);
    return generate(displayDate);
  };

  let previous = function() {
    displayDate.addMonths(-1);
    return generate(displayDate);
  };

  let nextYear = function() {
    displayDate.addYears(1);
    return generate(displayDate);
  };

  let previousYear = function() {
    displayDate.addYears(-1);
    return generate(displayDate);
  };

  let today = function() {
    return setDay(Date.create().reset());
  };

  let yesterday = function() {
    return setDay(Date.create("yesterday").reset());
  };

  let tomorrow = function() {
    return setDay(Date.create("tomorrow").reset());
  };

  let periodJumpSelection = null;
  let pushMode = 1;



  let setDay = function(day) {
    pushMode = 1;
    console.error(`setDay ${JSON.stringify(day)}, value: ${JSON.stringify(value)}`)
    return dispatch("change", day);
  };

  let periodJump = function() {

    var target, targetInterval;

    if (periodJumpSelection != null) {
      targetInterval = periodJumpSelection.interval + ` ${periodJumpSelection.unit}`;
      console.log(`Target interval ${targetInterval}`);

      target = displayDate.advance(targetInterval);    
      displayDate = displayDate;
      console.log(`Target date ${target}`);
      periodJumpSelection = null;
      setDay(target);
    }
  };



  let togglepushMode = function() {
    pushMode = pushMode * -1;
  };

  let setNextWeekday = function(weekday) {
    console.error(`setNextWeekday ${weekday}`);
    console.log(displayDate);

    var differential, target;
    if (weekday > displayDate.getWeekday()) {
      differential = weekday - displayDate.getWeekday();
    } else {
      differential = 7 - displayDate.getWeekday() + weekday;
    }
    target = displayDate.advance(differential + " days");
    displayDate = target
    value = target
    
    
    return setDay(target);
  };

  

  let setIsShowing = function(state) {
    if (!state) {
      pushMode = 1;
      return current(value);
    }
  };



  $: current(value);
  $: generate(displayDate);
  $: pushDirection = (pushMode == 1 ? "Forward" : "Back");
  


</script>





<div container data-value={value} data-display-date={displayDate}>
  <!-- day shortcuts -->
  <nav day-shortcuts>
    <FlexRow>
      <FlexRowCell style="width: calc(100% / 3)">
        <button type="button" on:click={yesterday} day-shortcut>
          Yesterday
        </button>
      </FlexRowCell>
      <FlexRowCell flex="flex" style="width: calc(100% / 3)">
        <button type="button" on:click={today} day-shortcut>
          Today
        </button>
        </FlexRowCell>
      <FlexRowCell style="width: calc(100% / 3)">
        <button type="button" on:click={tomorrow} day-shortcut>
          Tomorrow
        </button>

      </FlexRowCell>
    </FlexRow>
  </nav>

  <!-- month header -->
  <nav month-bar>
    <FlexRow>
      <FlexRowCell flex="flex">
        <h1>{currentCalendarHeader}</h1>
      </FlexRowCell>
      <FlexRowCell flex="initial">
        <ButtonIcon on:click={previousYear}>
          <IconSvg icon="previous-year"/>
        </ButtonIcon>
      </FlexRowCell>  
      <FlexRowCell flex="initial">
        <ButtonIcon on:click={previous}>
          <IconSvg icon="previous"/>
        </ButtonIcon>
      </FlexRowCell>  
      <FlexRowCell flex="initial">              
        <ButtonIcon on:click={next}>
          <IconSvg icon="next"/>
        </ButtonIcon>
      </FlexRowCell>  
      <FlexRowCell flex="initial">              
        <ButtonIcon on:click={nextYear}>
          <IconSvg icon="next-year"/>
        </ButtonIcon>
      </FlexRowCell>
    </FlexRow>
  </nav>


  <nav weekday-bar>
    <FlexRow>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(0)}">S</button></FlexRowCell>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(1)}">M</button></FlexRowCell>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(2)}">T</button></FlexRowCell>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(3)}">W</button></FlexRowCell>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(4)}">T</button></FlexRowCell>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(5)}">F</button></FlexRowCell>
      <FlexRowCell flex="initial" style="width: calc(100% / 7); text-align: center;"><button weekday on:click="{() => setNextWeekday(6)}">S</button></FlexRowCell>
    </FlexRow>
  </nav>
  {#each weeks as week, weekIndex}
    <FlexRow style="">

      {#each week as day, dayindex}
        {#if (parseInt(day.format("{M}")) != parseInt(currentMonth)) }
          {#if (weekIndex < 1) }
            <FlexRowCell style="width: calc(100% / 7)">
              <div class="day" month="previous" selected={day.is(value)}>
                <button on:click={setDay(day)}>{day.format("{d}")}</button>              
              </div>

            </FlexRowCell>
          {:else}
            <FlexRowCell style="width: calc(100% / 7)">
              <div class="day" month="next" selected={day.is(value)}>
                <button on:click={setDay(day)}>{day.format("{d}")}</button>
              </div>
              
            </FlexRowCell>
          {/if}
        {:else}
          <FlexRowCell style="width: calc(100% / 7)">
            <div class="day" month="current" today={day.isToday()} selected={day.is(value)}>
              <button on:click={setDay(day)}>{day.format("{d}")}</button>
            </div>
          </FlexRowCell>
        {/if}
      {/each}
    </FlexRow>
  {/each}

  {#if value}
  <nav period-jump>
    <FlexRow>
      <FlexRowCell flex="initial">
        <button on:click={togglepushMode} style="margin-right: 8px">     
          {#if pushMode == 1}
            <IconSvg icon="arrow-expand-right"/>
          {:else}
            <IconSvg icon="arrow-expand-left"/>
          {/if}      
        </button>
      </FlexRowCell>            
      <FlexRowCell flex="flex">
        <select bind:value={periodJumpSelection} on:change={periodJump}>

          <option value={null}>Skip { pushDirection }</option>

          <option value={{unit: "day", interval: (7 * pushMode)}}>
            { pushDirection }&nbsp;1 Week
          </option>
          <option value={{unit: "day", interval: (10 * pushMode)}}>
            { pushDirection }&nbsp;10 Days
          </option>
          <option value={{unit: "day", interval: (14 * pushMode)}}>
            { pushDirection }&nbsp;2 Weeks
          </option>
          <option value={{unit: "day", interval: (28 * pushMode)}}>
            { pushDirection }&nbsp;4 Weeks
          </option>
          <option value={{unit: "month", interval: (1 * pushMode)}}>
            { pushDirection }&nbsp;1 Month
          </option>
          <option value={{unit: "month", interval: (2 * pushMode)}}>
            { pushDirection }&nbsp;2 Months
          </option>
          <option value={{unit: "day", interval: (90 * pushMode)}}>
            { pushDirection }&nbsp;90 Days
          </option>
          <option value={{unit: "month", interval: (3 * pushMode)}}>
            { pushDirection }&nbsp;3 Months
          </option>
          <option value={{unit: "day", interval: (180 * pushMode)}}>
            { pushDirection }&nbsp;180 Days
          </option>
          <option value={{unit: "month", interval: (6 * pushMode)}}>
            { pushDirection }&nbsp;6 Months
          </option>
          <option value={{unit: "year", interval: (1 * pushMode)}}>
            { pushDirection }&nbsp;1 Year
          </option>
        </select>
      </FlexRowCell>
    </FlexRow>
      
  </nav>
  {/if}

</div>

<style>


  [container] {
    display: block;
    width: 280px;
    padding: 8px; }
    [container] nav[day-shortcuts] {
      border-top: 1px solid var(--lighter-gray);
      border-bottom: 1px solid var(--lighter-gray);
      font-weight: bold;
      margin: 0px 0px;
      padding: 12px 0px; }
      [container] nav[day-shortcuts] button {
        text-transform: uppercase;
        text-align: center;
        width: 100%; }
    [container] nav[month-bar] {
      padding: 12px 4px; }
      [container] nav[month-bar] h1 {
        line-height: 28px;
        margin: 0px;
        font-weight: bold;
        text-align: left;
        vertical-align: middle;
        padding: 0px;
        font-size: 16px;
        text-transform: uppercase; }
    [container] nav[weekday-bar] {
      padding: 6px 0px; }
    [container] button[weekday] {
      width: 100%;
      font-size: 16px;
      font-weight: bold;
      text-align: center; }
      [container] button[weekday]:hover {
        color: var(--blue); }
    [container] .day {
      text-align: center;
      width: 100%;
      height: 36px;
      font-size: 16px; }
      [container] .day button {
        width: 100%;
        height: 100%;
        font-size: 16px;
        text-align: center;
        vertical-align: middle;
        font-weight: bold;
        border-radius: 6px;
        border: 1px solid white; }
        [container] .day button:hover {
          background-color: var(--blue);
          color: var(--white) !important; }
      [container] .day[month=previous] button, [container] .day[month=next] button {
        color: var(--light-gray); }
      [container] .day[today="true"] button {
        color: var(--dark-blue); }
      [container] .day[selected="true"] button {
        background-color: var(--blue);
        color: var(--white); }
    [container] nav[period-jump], [container] nav[day-jump] {
      font-weight: bold;
      padding-top: 8px; }
      [container] nav[period-jump] button, [container] nav[day-jump] button {
        text-align: center;
        padding: 4px 0px;
        margin-right: 4px;
        height: 100%;
        font-weight: bold;
        text-transform: uppercase; }
        [container] nav[period-jump] button:hover, [container] nav[day-jump] button:hover {
          color: var(--dark-blue); }
    [container] nav[period-jump] {
      margin-top: 8px;
      border-top: 1px solid var(--lighter-gray); }
      [container] nav[period-jump] select {
        font-weight: normal; }
    [container] nav[day-jump] {
      margin-bottom: 8px; }



</style>
