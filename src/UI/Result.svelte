<script>
    import { question, unattempted,attempted,allques,currentcorrect,allincorrect,current,selectedanswer,disable1,disable2,correctques,timetaken } from "../dataStore";
    import Nav from "./Navigation.svelte";
    import Review from "./Review.svelte";
    let Heading;
    let minutes = 0;
    let Raw_Unattempted;
    let result = 0.0;
    let showall = false;
    let showcorrect = false;
    let showincorrect = false;
    let count = 0;
    let dummyarray = [];
    let correctlength;
    let review = false;
    let currentselect = [];
    let incorrect = [];
    let Current_Correct = [];
    let showunattempt = false;
    let questioncorrect = [];
    let secs;
    let min;
    if ($timetaken) {
      min = $timetaken / 60;
      minutes = Math.trunc(min);
      secs = $timetaken % 60;
    }
    else if($timetaken==0) {
      secs=0;
    }
    attempted.subscribe((items) => {
      let Duplicate = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      dummyarray = [...Duplicate];
      count = Duplicate.length;
    });
    unattempted.subscribe((items) => {
      let Duplicate = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      Raw_Unattempted = Duplicate.length;
    });
    currentcorrect.subscribe((items) => {
      let Duplicate = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      Current_Correct = [...Duplicate];
      correctlength = Duplicate.length;
    });
    $: correctlength = correctlength;
    $: result = Math.round((correctlength / 11) * 100);
    function goReview(jump) {
      review = true; //Here some issue
      current.update((its) => {
        return jump;
      });
      if (jump == 0) {
        $disable2 = true;
      }
      if (jump > 0) {
        disable1.set(false);
      }
    }
    selectedanswer.subscribe((items) => {
      let Duplicate = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      currentselect = [...Duplicate];
    });
    allincorrect.subscribe((items) => {
      let Duplicate = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      incorrect = [...Duplicate];
    });
    correctques.subscribe((items) => {
      let Duplicate = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      questioncorrect = [...Duplicate];
    });
  </script>
  
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css"/>
  <link rel="stylesheet" href="style.css" />
  <header>
    <Nav Heading={"uCertify Test Result"} />
  </header>
  <div class="container">
    <div class="result-item" tabindex="0">
      <b class="result">
        {result}%
      </b><br/>Result
    </div>
    <div class="result-item" tabindex="0" on:click={() => {showall = true;showcorrect = false;showincorrect = false;showunattempt = false;}}>
      <b class="all">
        {$allques.length}
      </b><br/>All Items
    </div>
    <div class="result-item" tabindex="0" on:click={() => {showcorrect = true;showincorrect = false;showall = false;showunattempt = false;}}>
      <b class="correct">
        {correctlength}
      </b><br />Correct
    </div>
    <div class="result-item" tabindex="0" on:click={() => {showincorrect = true;showcorrect = false;showall = false;showunattempt = false;}}>
      <b class="incorrect">
       {count - correctlength}
      </b><br />Incorrect
    </div>
    <div class="result-item" tabindex="0" on:click={() => {showunattempt = true;showcorrect = false;showincorrect = false;showall = false;}}>
      <b class="unattempt">
        {11 - count}
      </b><br />Unattempted
    </div><br />
  </div>
  <p class="time font-700" tabindex="0">Time Taken: {minutes}min :{secs}sec</p>
  <div class="table">
    <table>
      <tr>
        <th class="first" tabindex="0">Index No</th>
        <th class="second" tabindex="0">Questions</th>
        <th class="third" tabindex="0">Answers</th>
      </tr>
      {#each $question as dataItem, i (dataItem)}
        <tr class:hidecorrect={showcorrect &&(!questioncorrect.includes(JSON.parse(dataItem.content_text).question) ||
          !dummyarray.includes(JSON.parse(dataItem.content_text).question))} class:hideincorrect={showincorrect &&
          (questioncorrect.includes(JSON.parse(dataItem.content_text).question) || !dummyarray.includes(JSON.parse(dataItem.content_text).question))}
          class:show={showall && (questioncorrect.includes(JSON.parse(dataItem.content_text).question) || !questioncorrect.includes(
          JSON.parse(dataItem.content_text).question) || !dummyarray.includes(JSON.parse(dataItem.content_text).question) ||
          !dummyarray.includes(JSON.parse(dataItem.content_text).question))} class:un={showunattempt && dummyarray.includes(JSON.parse(dataItem.content_text).question)}>
          <td class="center" tabindex="0">{i + 1}</td>
          <!-- svelte-ignore a11y-accesskey -->
          <td id="questions" tabindex="0" accesskey={i+1} on:click={goReview(i)}>{JSON.parse(dataItem.content_text).question}</td>
          <td>
            <div class="center">
              {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
                <span  is_correct={ans.is_correct} class="dot" class:success={($selectedanswer.includes(ans.answer) && ans.is_correct == 1) ||
                  ans.is_correct == 1} class:unsuccess={$selectedanswer.includes(ans.answer) && ans.is_correct == 0} id="ans{index}"
                  value={ans.answer}>
                  {#if ans.is_correct==1}
                    <span tabindex="0" role="button" aria-label="Option{index+1} is Correct">{index + 1}</span>
                  {:else if ans.is_correct==0}
                    <span tabindex="0" role="button" aria-label="Option{index+1} is InCorrect">{index + 1}</span>
                  {/if}
                </span>
              {/each}
              {#if !dummyarray.includes(JSON.parse(dataItem.content_text).question)}
                <div class="tooltip">
                  <i  tabindex="0" role="button" aria-label="Unattempted" />
                  <span class="tooltiptext">Unattempted</span>
                </div>
              {/if}
              {#if dummyarray.includes(JSON.parse(dataItem.content_text).question) && questioncorrect.includes(JSON.parse(dataItem.content_text).question)}
                <div class="tooltip">
                  <i class="fa fa-check" tabindex="0" role="button" aria-label="correct" />
                  <span class="tooltiptext">Correct</span>
                </div>
              {:else if dummyarray.includes(JSON.parse(dataItem.content_text).question) && !questioncorrect.includes(JSON.parse(dataItem.content_text).question)}
                <div class="tooltip">
                  <i tabindex="0" role="button" aria-label="incorrect"/>
                  <span class="tooltiptext">Incorrect</span>
                </div>
              {/if}
            </div>
          </td>
        </tr>
      {/each}
    </table>
  </div>
  {#if review}
    <Review />
  {/if}