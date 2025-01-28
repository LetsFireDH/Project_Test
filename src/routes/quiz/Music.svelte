<script lang="ts"> 
//TypeScript wird hier benutzt, damit die Lautstärkensteuerung funktioniert, bei JavaScript würde es schreiben, dass die Methoden .play und .pause nicht für alle HTML Elemente existieren. Nur über TypeScript lässt sich der Typ definieren.
//Dieser Vorschlag kommt von GPT4o.
  import Kahoot from "./lib/Lobby Music (Original Soundtrack).mp3";
  let audiovolume = $state(58); //Setzt die Lautstärke auf 100%
  let isplaying = $state(false); //Schaut ob die Musik gerade am laufen ist, am Anfang läuft sie natürlich nicht

  function playpauseaudio(){
    let audiohtml = document.getElementById("music") as HTMLAudioElement; //Hier lässt sich exakt definieren, dass es sich um ein Audio-Element handelt. Damit lässt sich die Fehlermeldung mit den beiden Methoden umgehen.
    if (isplaying === false) {
      audiohtml.play(); //Hier wird der Ton abgespielt
      isplaying = true //Der Ton wird abgespielt, die Variable wird also entsprechend gesetzt
    } else { 
      audiohtml.pause(); //Wenn auf Pause gedrückt wird, sollte die Musik auch pausieren
      isplaying = false //Die Variable wird auch hier entsprechend gesetzt.
    }
}
</script>

<main>
  <div>
    <!-- Lautstärkensteuerung-->
    <p>Lautstärke der Musik: {audiovolume}%</p>
    <label>
      <input type="range" bind:value={audiovolume} min="0" max="100" step="1" /> <!-- Ein Slider um die Lautstärke zu setzen -->
    </label>
  </div>
  <!-- loop Attribut aktiviert Endlosschleife: https://www.w3schools.com/tags/att_audio_loop.asp-->
  <audio id="music" controls style="display:none" volume={audiovolume / 100} loop> <!-- volume Attribut setzt die Lautstärke für das Audio-Element. Mithilfe von svelte können wir direkt die Lautstärkenzahl der inputs weitergeben.-->
    <source
      src={Kahoot}
      type="audio/mpeg"
    />
  </audio>
  <button id="playpause" onclick={() => playpauseaudio()}>{isplaying ? "Pause" : "Play"} Music</button> <!-- Mithilfe des Ternery Operators können wir geschickt überprüfen, ob Pause oder Play angezeigt werden muss. -->
</main>

<style>
  main {
    text-align: center; /* Alles zentrieren */
    align-items: center;
    justify-content: center;
  }
  button {
    background-color: #535353; /* setzt die Hintergrundfarbe*/
    color: white; /*setzt die Textfarbe*/
    width: 500px; /*Die Weide des Knopfs */
    margin-bottom: 8px;
  }
  p {
    color: white;
    font-size: 17px;
    margin-right: 15px;
  }
  div, label {
    display:flex; /*Aktiviert die flex-Anzeige */
    flex-direction:row; /* Diese wird verwendet, um die Elemente nebeneinander anzuzeigen, anstatt untereinander. Die Richtung row gibt das an */
    justify-content:center; /*Schaut, dass Text und Regler auf selber Höhe stehen*/
    align-items:center
  }
  #playpause {
    margin-top: 10px;
  }
</style>
