<script>
  //Import der Schwierigkeitsgradbilder (Import wie bei der Vite-Svelte Demo-Seite [App1.svelte])
  import easy from "./assets/easy.png";
  import medium from "./assets/medium.png";
  import hard from "./assets/hard.png";
  import { score, showscreen, questionamount } from "./shared.svelte.js" //Importiert die Punktzahl, die Sichtbarkeit der Startseite und die Anzahl der Fragen
  import { onMount } from "svelte"; //Importiert die onMount-Funktion, wie im Katzen-Fakten Website

  const quizurl = "https://opentdb.com/api.php?amount=" + questionamount.amount + "&type=multiple"; //Definiert die URL der API
  let questioncount = $state(0); //Welche Frage ist nun?
  let answerarrays = $derived([]); //Speichert alle Antworten einer Frage. Derived, weil es vom Update des Question Array abhängt. (und nicht $state) 
  let nextvisible = $state(false); //Steuert die Sichtbarkeit des Knopfs zum Weiterdrücken
  const buttonarray = ["answer1", "answer2", "answer3", "answer4"]; //Erleichtert das Deaktivieren und Aktivieren der Antwortknöpfe
  const correct_answerbackground = "rgb(0, 123, 0)";
  const wrong_answerbackground = "rgb(202, 0, 0)";

  import { get } from "svelte/store";

  let promise = $state(); //Definiert das "Versprechen" des Pakets
  onMount(async () => {
    //Sorgt, dass die API schon beim Laden der Seite gefetcht wird
    promise = fetchQuiz();
  });

  const fetchQuiz = async () => {
    try {
    //Diese Funktion fetcht die API
    const res = await fetch(quizurl); //Fetcht die API
    const question = await res.json(); //Wandelt das Websiten-Promise in ein JSON um
    console.log(question);

    if (question) {
      return question; //Gibt das Ergebnis zur Weiterverarbeitung zurück
    }
  } catch (error) { //Error Handling
    console.log(error);
    document.getElementById("error").innerHTML = "Es ist ein Fehler aufgetreten. Bitte überprüfen Sie ihre Internetverbindung.";

  }
  };

  function showDifficulty(question, questioncountInt) {
    switch (question.results[questioncountInt].difficulty) { //Abkürzung für if question.results[questioncountInt].difficulty === "easy" ==="medium" und ==="hard"
      case "easy":
        return easy; //Wenn die Schwierigkeit vom JSON-Objekt easy ist, wird das Bild mit einem Stern, das bereits oben geladen ist, dem img-Tag zurückgegeben. So wird das richtige Bild angezeigt.
      case "medium":
        return medium;
      case "hard":
        return hard;
    }
  }

  function shuffleanswers(question, questioncountInt) {
    try {
    //Alle Antworten löschen
    for (let t = 0; t < 5; t++) {
      answerarrays.pop();
    }

    // Alle Antworten in ein Array schreiben
    for (let i = 0; i < question.results[questioncountInt].incorrect_answers.length; i++) {
      answerarrays.push(
        question.results[questioncountInt].incorrect_answers[i]
      );
    }
    answerarrays.push(question.results[questioncountInt].correct_answer);

    //Array durchmischen und shuffeln (von GPT 4o)
    for (let i = answerarrays.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [answerarrays[i], answerarrays[j]] = [answerarrays[j], answerarrays[i]];
    }
    console.log(answerarrays);

    //Antworten updaten
    if (questioncount > 0) {
      document.getElementById("answer2").innerHTML = answerarrays[1];
      document.getElementById("answer3").innerHTML = answerarrays[2];
      document.getElementById("answer4").innerHTML = answerarrays[3];
    }

    return answerarrays;
  } catch (error) { //Fehler Handling
    console.log(error);
    console.log("Die Fragen konnten nicht gemischt werden.")
  }
  }

  function checkcorrectanswer(question, questioncount, ans) {
    //Knöpfe deaktivieren, wenn eine Antwort ausgewählt worden ist.
    for (let i in buttonarray) {
      document.getElementById(buttonarray[i]).setAttribute("disabled", "");
    }

    //Überprüfen, ob die gewählte Antwort mit der richtigen übereinstimmt.
    if (answerarrays[ans] === question.results[questioncount].correct_answer) {
      score.score += 1; //Punkte aktualisieren
      document.getElementById(buttonarray[ans]).style.backgroundColor = correct_answerbackground; //Richtige Antwort markieren
      console.log("Correct");
    } else {
      //Falsche Antwort ausgewählt
      document.getElementById(buttonarray[ans]).style.backgroundColor = wrong_answerbackground; //Falsche Antwort markieren

      //Richtige Antwort heraussuchen und markieren
      for (let i in buttonarray) {
        //Da die Antworten aus Answerarray mit der Stelle von buttonarray übereinstimmen, können wir den Index für beide Arrays gleichzeitig verwenden.
        if (answerarrays[i] === question.results[questioncount].correct_answer) {
          //Sucht die richtige Stelle der Antwort heraus und speichert sie in i
          document.getElementById(buttonarray[i]).style.backgroundColor = correct_answerbackground; //Richtige Antwort markieren (befindet sich an der i. Stelle)
        }
      }
      console.log("Wrong");
    }
    nextvisible = true; //Nun kann man weiterklicken
    return;
  }

  function nextquestion(question) {
    if (questioncount === question.results.length - 1) {//Wenn wir bei der letzten Frage angekommen sind und es zum Endscreen gehen soll.
      showscreen.endscreen = true;
    } else { //Sonst nächste Frage anzeigen
    //Knöpfe wieder aktivieren
      for (let i in buttonarray) {
        document.getElementById(buttonarray[i]).removeAttribute("style");
        document.getElementById(buttonarray[i]).removeAttribute("disabled");
      }
      questioncount += 1;
      nextvisible = false;
      return;
  }
  }

  const reload = () => {
    promise = fetchQuiz(); //Fetcht die API beim Neuladen der Seite (von der Katzen-Fakten Seite)
  };
</script>

<main>
  <div class="question" style="align-items:left">
    <p style="font-size:27px">Frage: {questioncount + 1} Punkte: {score.score}</p>
    <!-- Zeigt die aktuelle Frage an -->
    {#await promise}
      <!-- Wie das fetch im Java Script -->
      <p>Loading...</p>
    {:then question}
      <!-- Hier wird mit Question von fetchQuiz() weitergearbeitet.-->
      <!-- Hier werden die Fragen ausgelesen und in HTML formatiert -->
      <div id="questioninformation">
        <p id="category" class="width-restriction" style="font-size:22px">{@html question.results[questioncount].category}</p><br>

        <div class="difficulty" style="display:flex; flex-direction:row">
          <p id="difficulty" class="width-restriction">Difficulty:</p>
          <img src={showDifficulty(question, questioncount)} alt="difficulty" style="width:105px; height:30px; display:flex">
        </div>
      </div>

      <p id="question_mult" class="width-restriction">{@html question.results[questioncount].question}</p>

      <button id="answer1" onclick={() => checkcorrectanswer(question, questioncount, 0)}>{@html shuffleanswers(question, questioncount)[0]}</button> <br />
      <button id="answer2" onclick={() => checkcorrectanswer(question, questioncount, 1)}>{@html answerarrays[1]}</button> <br />
      <button id="answer3" onclick={() => checkcorrectanswer(question, questioncount, 2)}>{@html answerarrays[2]}</button> <br />
      <button id="answer4" onclick={() => checkcorrectanswer(question, questioncount, 3)}>{@html answerarrays[3]}</button> <br />

      {#if nextvisible === true}
        <!-- Zeigt den Knopf an, wenn er sichtbar sein sollte.-->
        <button onclick={() => nextquestion(question)} id="next">Next</button>
      {/if}
    {/await}
    <p id="error"></p>
  </div>

</main>

<style>
  :root {
    --first-color: #04f1a2;
  }
  main {
    background-color: #0f0f0f;
  }
  p {
    color: #d6d6d6;
  }
   
  #difficulty {
    font-size:22px;
    display:flex;
    margin-right:10px
  }
  #error {
    font-size: 24px;
  }
  #question_mult {
    background: linear-gradient(to right, var(--first-color), #b5d42c, #dd2ddd);
    -webkit-text-fill-color: transparent;
    background-clip: text;
    -webkit-background-clip: text;
    font-size: 32px;
    margin-top: 2px;
    margin-bottom: 50px;
  }

  #category, #difficulty {
    color: var(--first-color);
    margin-top: 0px;
    margin-bottom: 2px;
  }


  button {
    background-color: #535353;
    color: white;
    width: 800px;
    margin-bottom: 8px;
  }

  button:disabled {
    background-color: #929292;
  }
  .width-restriction {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
</style>
