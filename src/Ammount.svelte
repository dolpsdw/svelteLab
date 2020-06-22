<script>
  /** configurable separator , and .
   *   +- characters not allowed
   * edgeCases:
   * inputType: "insertFromPaste"
   * 000000000000000024
   * 3,,,,,
   * 3e3e3e3e3e
   */
  export let riskAble = true;
  export let winAble = true;
  export let decimalChar = ",";

  let lastValidWin = 0;
  let lastValidRisk = 0;

  let currentWin = 0;
  let currentRisk = 0;

  const handleWinInput = (e) => {
    console.log("handledWinStart", e);
    if (isNaN(e.target.valueAsNumber)) {
      // [+, -, e] trigger this
      console.log(lastValidWin);
      currentWin = lastValidWin + 1 - 1;
      // currentWin reAsignation does not work on this update cycle
      // Javascript to the rescue
      e.target.value = lastValidWin;
      // And this is not firing handleWinInput again
    } else {
      lastValidWin = e.target.valueAsNumber;
    }
  };
  const handleRiskInput = (e) => {
    console.log("handledRiskStart", e);
    if (isNaN(e.target.valueAsNumber)) {
      console.log(lastValidRisk);
      currentRisk = lastValidRisk;
      e.target.value = lastValidRisk;
    } else {
      lastValidRisk = e.target.valueAsNumber;
    }
  };
</script>

<!-- https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/number -->
<!-- input event get fired each time text value changes -->
<input
  type="number"
  disabled={!riskAble}
  min="1"
  on:input={handleWinInput}
  bind:value={currentWin} />
<input
  type="number"
  disabled={!winAble}
  min="1"
  on:input={handleRiskInput}
  bind:value={currentRisk} />
