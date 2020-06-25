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
    if (e.inputType === "deleteContentBackward" && lastValidWin < 10) {
      // single digit delete will put a 0
      e.target.value = 0;
    }
    const prevValidWin = lastValidWin; //needed for getCarretSelection
    if (!isNaN(e.target.valueAsNumber)) {
      // ![+, -, e] trigger this
      lastValidWin = e.target.valueAsNumber;
    }
    // Javascript to the rescue also reformat 00000000 case
    e.target.value = lastValidWin.toFixed(2);
    const lastValidWinString = lastValidWin.toString();
    e.target.setAttribute("type", "text");
    if (e.data === ",") {
      // If , pressed
      e.target.setSelectionRange(
        lastValidWinString.length + 1,
        lastValidWinString.length + 1
      );
    } else {
      const carret = getCarretSelection(
        prevValidWin,
        lastValidWin,
        e.inputType
      );
      e.target.setSelectionRange(carret, carret);
    }
    e.target.setAttribute("type", "number");
    // And this is not firing handleWinInput again
  };
  const handleRiskInput = (e) => {
    console.log("handledRiskStart", e);
    if (e.inputType === "deleteContentBackward" && lastValidWin < 10) {
      // single digit will put a 0
      e.target.value = 0;
    }
    if (!isNaN(e.target.valueAsNumber)) {
      // ![+, -, e] trigger this
      lastValidRisk = e.target.valueAsNumber;
    }
    // Javascript to the rescue also reformat 00000000 case
    e.target.value = lastValidRisk;
    // And this is not firing handleRiskInput again
  };
  function getCarretSelection(prevValidWin, lastValidWin, inputType) {
    if (prevValidWin === lastValidWin) {
      return lastValidWin.toString().length;
    }
    const prevValidWinString = prevValidWin.toFixed(2);
    const lastValidWinString = lastValidWin.toFixed(2);
    let i = 0;
    while (prevValidWinString[i] === lastValidWinString[i]) i++;
    if (inputType.includes("delete")) {
      return i;
    }
    return i + 1;
  }
  /*Reactive Blocks $: calculatedRisk*/
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
