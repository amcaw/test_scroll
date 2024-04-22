<script>
  import { onMount, onDestroy } from 'svelte';

	import LoremIpsum from './LoremIpsum.svelte'

	import Introduction from './Introduction.svelte'

  let activeStepIndex = 0;
  let observer;
  let scrollyRef;
  let flourishID = "2323932"; // L'ID de la story Flourish

// le texte des boites
	
  let stepsData = [
    { "text": "L'agriculture bio n'était pas très populaire <mark style='background-color: #77bb41; color:white; padding: 2px; border-radius: 5px;'><strong>dans les années 2000</strong></mark>, mais connait un regain en 2004." },
    { "text": "Un véritable boom <mark style='background-color: #77bb41; color:white; padding: 2px; border-radius: 5px;'><strong>à partir de 2005</strong></mark>, jusqu'en 2013."},
    { "text": "L'agriculture Bio n'a cessée de croitre<mark style='background-color: #77bb41; color:white; padding: 2px; border-radius: 5px;'><strong>depuis 2014</strong></mark>, pour atteindre un pic en 2022."},
  ];

	// Le "moteur" du scrollytelling qui utilise l'Intersection Observer API (en gros, le code observe ce qu'il y a à l'écran)
	// ça on ne touche pas sinon tout se casse !

  onMount(() => {
    observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        const { target, isIntersecting } = entry;
        const index = Array.from(scrollyRef.querySelectorAll('.step')).indexOf(target);
        if (isIntersecting) {
          activeStepIndex = index;
        }
      });
    }, { threshold: 0.6 });

    const stepElements = scrollyRef.querySelectorAll('.step');
    stepElements.forEach(el => observer.observe(el));
  });

  onDestroy(() => {
    observer.disconnect();
  });
</script>

<h1><u>L'évolution de l'agriculture bio en 20 ans</u></h1>


<Introduction/>

<!-- si tu affiches la step_0, alors montre la slide_0 -->
<!-- si tu affiches la step_1, alors montre la slide_1 -->
<!-- si tu affiches la step_2, alors montre la slide_2 -->

<section bind:this={scrollyRef} class="section-container">

	  <div class="foreground">
    {#each stepsData as { text }, index}
      <div class="step" data-step={index}>
        <div class="step-content">
          <p>{@html text}</p> <!-- @html important pour que le css du script soit pris en compte-->
        </div>
      </div>
    {/each}
  </div>
	
  <div class="sticky-background">
    <!-- On affiche la slide Flourish en fonction de l'index -->
		<iframe src={`https://flo.uri.sh/story/${flourishID}/embed#slide-${activeStepIndex}`} title="Contenu interactif ou visuel" class="flourish-embed" frameborder="0" scrolling="no" style="width:100%;height:100vh;"></iframe>
  </div>


</section>


<style>

	/* Ici les valeurs pour l'ensemble de la page > 
	peut nécessiter des modifs de couleurs dans Flourish 
	pour s'assurer que le graphe soit tjs bien visible (titre de graphique noir sur
	fond de page noir,ça ne se voit pas bien...*/

	:global(body) {
    background-color: lightgrey; 
		color: black;
		font-family: 'calibri', sans-serif; 
  }
	
	*{
		box-sizing: border-box;
	}
	.section-container {
    margin-top: 1em;
    text-align: center;
    display: flex;
    flex-direction: row; 
		
  }

  .sticky-background {
    position: sticky;
    top: 0;
    height: 100vh;
    flex: 0 1 60%;
    overflow: hidden;
    z-index: 1;
  }

  .foreground {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 0 1 40%;
		margin-bottom: 100vh;
  }

  .step {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    position: relative;
    z-index: 2;
    width: 100%;
  }

  .step-content {
    background-color: rgba(245, 245, 245, 0.8);
		box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
    color: black;
    border-radius: 9px;
		border: 1px solid green;
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    z-index: 10;
    font-size: 1rem;
    text-align: left;
    width: 100%; 
    max-width: 500px; 
    margin: auto;
  }

	/* Pour adapter la vue en mobile: steps centrées par dessus le graphique */

  @media screen and (max-width: 768px) {
    .section-container {
      flex-direction: column-reverse;
    }
    .sticky-background, .foreground {
      width: 100%; 
    }
    .foreground {
      margin-top: -80vh; 
    }
  }
</style>

