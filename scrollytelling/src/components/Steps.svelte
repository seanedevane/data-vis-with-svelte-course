<script>
    import Scrolly from "../utilities/Scrolly.svelte";

    export let currentStep;
</script>

<div class='steps'>
    <Scrolly bind:value={currentStep}>
      {#each ['Hello', 'scrollytelling', 'world'] as text, i}
        <div class='step' class:active={currentStep === i }>
          <div class='step-content'>
            <p>{text}</p>
          </div>
        </div>
      {/each}
    </Scrolly>
    </div> <!-- end scrolly .steps div -->

<style>
.steps {
    position: relative;
    z-index: 2;
    pointer-events: none;
}
.step {
    height: 90vh;
    opacity: 0.3;
    transition: opacity 300ms ease;
    display: flex;
    justify-content: center;
    place-items: center;
}
.step.active {
    opacity: 1;
}
.step-content {
    background: white;
    border: 1px solid black;
    padding: 1rem 1.25rem;
    border-radius: 0.3rem;
}
</style>