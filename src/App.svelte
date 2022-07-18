<script>
  let backgroundColor
  let message = ''
  let errorMessage = ''
  let isGatheringResult = false

  const resetAppState = () => {
    backgroundColor = null
    message = ''
    errorMessage = ''
    isGatheringResult = false
  }

  const recognition = new (window.SpeechRecognition ||
    window.webkitSpeechRecognition ||
    window.mozSpeechRecognition ||
    window.msSpeechRecognition)()

  recognition.lang = 'en-US'

  recognition.onresult = event => {
    backgroundColor = event.results[0][0].transcript.replace(' ', '')
    message = `Your color is: ${backgroundColor}.`
  }

  recognition.onerror = event => {
    resetAppState()

    // prettier-ignore
    const errorLookup = {
      'no-speech': 'No speech was detected.',
      'not-allowed': 'Website does not have access to microphone',
      'aborted': 'Speech Recognition was aborted',
      'audio-capture': 'Was not able to read audio',
      'network': 'Internet Required for speech recognition',
      'service-not-allowed': 'Speech Recognition is not allowed',
      'bad-grammar': 'You have bad grammer, innit',
      'language-not-supported': 'Langauge not supported',
    }

    errorMessage = errorLookup[event.error] || 'Gee, I dunno.'
  }

  recognition.onspeechend = event => (isGatheringResult = false)

  const handleClick = () => {
    if (isGatheringResult) return

    recognition.start()
    isGatheringResult = true
    message = 'Say a CSS color!'
  }
</script>

<main>
  <h1>Speech Recognition Color Changer</h1>

  <div style="background-color:{backgroundColor}" />

  <button on:click={handleClick}>Say Color</button>

  <p>{message}</p>

  <aside>{errorMessage}</aside>
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 2rem;
  }
  div {
    width: 300px;
    height: 300px;
    border: black dashed 3px;
  }
  aside {
    color: red;
  }
</style>
