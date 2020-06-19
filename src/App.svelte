<script>
import Test from "./Comp.svelte";

  export let name;
  import Comp from "./Comp.svelte"
</script>

<style>
  h1 {
    font-family: "Comic Sans MS";
  }
</style>

<h1>Hello {name}!</h1>
<Comp></Comp>
