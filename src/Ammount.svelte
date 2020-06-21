<script>
  /** configurable separator , and .
   *   +- characters not allowed
   */
  export let riskAble = true;
  export let winAble = true;
  const decimalChar = ",";
</script>

<input type="number" disabled={!riskAble} />
<input type="number" disabled={!winAble} />
