<template>
  <div>
    <center>
      <h1>Quiz App</h1>
    </center>
    <hr class="my-4" />
    <center>
      <h3>Correct answers {{ numCorrect }} of {{ numTotal }}</h3>
    </center>
    <br />
  </div>
</template>

<script>
export default {
  props: ["numCorrect", "numTotal"],
};
</script>