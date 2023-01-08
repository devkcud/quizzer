<script lang="ts">
  import { onMount } from 'svelte';

  export let category = 'Category';
  export let difficulty = 'Difficulty';
  export let question = 'Question';
  export let answer = 'True';
  export let points = 0;

  function unescapeHTML(str: string) {
    return str
      .replaceAll(/&amp;/g, '&')
      .replaceAll(/&lt;/g, '<')
      .replaceAll(/&gt;/g, '>')
      .replaceAll(/&quot;/g, '"')
      .replaceAll(/&ldquo;/g, '"')
      .replaceAll(/&rdquo;/g, '"')
      .replaceAll(/&minus;/g, '-')
      .replaceAll(/&#039;/g, "'")
      .replaceAll(/&eacute;/g, 'é');
  }

  export async function generateQuestion() {
    const req = await (await fetch('https://opentdb.com/api.php?amount=1&type=boolean')).json();
    const result = req['results'][0];

    question = unescapeHTML(result['question']);
    category = result['category'];
    difficulty = result['difficulty'];
    answer = result['correct_answer'];
  }

  export function checkAnswer(e: any) {
    e.target.innerHTML === answer ? (points += 1) : (points -= 1);
    generateQuestion();
  }

  onMount(() => {
    generateQuestion();
  });
</script>

<svelte:head>
  <title>Quizzer</title>
</svelte:head>

<main>
  <div class="container">
    <h1>{category} <span id="difficulty">{difficulty}</span></h1>
    <h2>{question}</h2>

    <div class="d-flex">
      <button class="answerbtn" id="answer-true" on:click={checkAnswer}>True</button>
      <button class="answerbtn" id="answer-false" on:click={checkAnswer}>False</button>
    </div>
  </div>

  <h3 id="points">{points} points</h3>
  <button class="bigger" on:click={generateQuestion}>New Question</button>
</main>

<style lang="scss">
  main {
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 5rem;
  }

  h1 {
    margin: 2rem;
    text-align: center;
    position: relative;

    span#difficulty {
      font-size: smaller;

      color: #777;

      font-style: italic;
    }

    &::after {
      content: '';
      position: absolute;
      left: 0;
      bottom: -0.5rem;

      width: 100%;
      height: 1px;

      background-color: #ddd;
    }
  }

  h2 {
    text-align: center;

    margin-block: 3rem;
  }

  div.d-flex {
    display: flex;

    align-items: center;
    justify-content: center;

    gap: 5rem;
  }

  button {
    padding: 1rem;
    width: 5rem;
    scale: 1.5;

    color: #000;

    outline: none;

    border: none;
    border-radius: 0.2rem;

    transition: all 0.2s;

    &#answer-true {
      background-color: #77dd77;
    }

    &#answer-false {
      background-color: #ff6961;
    }

    &.bigger {
      width: fit-content;
    }

    &:hover {
      cursor: pointer;
      scale: 1.75;
    }
  }
</style>
