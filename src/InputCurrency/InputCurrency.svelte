<script>
  //This is the Spec zone
  /*
  1- this ALWAYS have to have a valid numeric Value and be in sync with the number displayed (0.00 by default)
  2- only keys that should be able to operate on it are [0-9] and [, .] ---- Beware of the + - e Infinty  e1e+-..,.,.,. ...
  3- Prevent Paste and select and cut of multi values
  4- Only accept Input Events that change the previus value in 1 digit.
  5- Discard Input events from type contains drag or drop
  */
  /* Tips:
  selectionStart and selectioncapable inputs text|search|password|tel|url
  change the type of the e.target to selectioncapable on focus will reset the carret.
  change the type of the e.target from selectioncapable to number will let you use setSelection range https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement
  value ALWAYS have to be a new Big().toFixed => this ensures that the internal decimal separator is .
  Dont use the svelte bind to value on input number, since it will be undefined is not a number and missbehave in certain situations.
  Use target.value to access the real and current text on the input number
  Maximicing the compativility: input type=number, onFocustypeTel inputmode=decimal
 */
  import Big from "big.js";
  export const fixedDigits = 2;

  let prevValue = new Big("0").toFixed(fixedDigits); // Not $reactive updated only when new valid input
  let userSelected = true;
  let prevCarret = 0;

  function handleSelect(e) {
    // Dont do Focus Blur that will break , . Fixed switch handler
    if (userSelected) {
      const temp = e.target.value; // you can not trust value prop to be upToDated onSelect
      e.target.value = "";
      e.target.value = new Big(temp).toFixed(fixedDigits); // This not trigger Input Events
    }
    userSelected = true;
  }
  function handleInput(e) {
    // value will be undefined when the text on the input is not a number so use e.target.value
    if (isNullUndefEmpty(e.target.value)) {
      // In case of deletion reset to 0
      const zero = new Big(0).toFixed(fixedDigits);
      e.target.value = zero;
      prevValue = zero;
      setCarret(e.target, 1); //0|.00
      return;
    }

    const prevValueAsBig = new Big(prevValue).toFixed(fixedDigits);
    if (Number.isNaN(+e.target.value)) {
      //In case of new Value not being a valid number
      e.target.value = prevValueAsBig; //Fallback to previus value
      setCarret(e.target, prevCarret);
      return;
    }
    const valueAsBig = new Big(e.target.value).toFixed(fixedDigits);
    const result = Math.abs(
      valueAsBig.toString().length - prevValueAsBig.toString().length
    ); // 4 == 4 first input with a diference of 0, the next valid inputs can differ on 1 digit only
    if (result > 1) {
      // If , . delete or if select and press key multiple units will change
      e.target.value = prevValueAsBig;
      setCarret(e.target, prevCarret);
      return;
    }
    //0.01 -> 1.00 same number of digits but change both the decimal and the integer part
    const [valueInt, valueFloat] = valueAsBig.toString().split(".", 2);
    const [prevValueInt, prevValueFloat] = prevValueAsBig
      .toString()
      .split(".", 2);
    if (valueInt !== prevValueInt && valueFloat !== prevValueFloat) {
      e.target.value = prevValueAsBig;
      setCarret(e.target, prevCarret);
      return;
    }
    // At this point the new value is a valid input
    //LEts try to hijack the carret someHow to solve 6000 -> 60000 in what place the 0 is inserted ?
    const selection = window.getSelection();
    //In case Navigator is compatible with selection api
    let carretPos;
    if (selection) {
      if (selection.type === "Caret" && selection.rangeCount === 1) {
        //In case the selection is a single Caret, and is active
        selection.modify("extend", "backward", "line"); // select all the line
        const carretSelection = selection.toString(); // This will have 01 string at start from  01,00
        if (isNullUndefEmpty(carretSelection)) {
          carretPos = undefined; // In case |,00 the carretSelection will be "" or in case of 1|23,45 delete 1 BUT what happen when ,|00 and delete ,?
        } else {
          let removedPrependedZeros;
          if (carretSelection.includes(",") || carretSelection.includes(".")) {
            removedPrependedZeros = new Big(carretSelection);
          } else {
            removedPrependedZeros = new Big(carretSelection);
          }
          carretPos = removedPrependedZeros.length;
        }
      }
    }
    // Reformat current input and put the carret
    e.target.value = valueAsBig;
    const carret = getCarretSelection(
      prevValueAsBig,
      valueAsBig,
      e.inputType,
      prevCarret
    );
    setCarret(e.target, carretPos ? carretPos : carret);
    // Safe the valid value in previus
    prevValue = valueAsBig;
    prevCarret = carret;
  }
  //afterUpdate(() => console.log("afterUpdate", value));
  //beforeUpdate(() => console.log("beforeUpdate", value)); // This does not work properly value is already changed so we need prevValue
  /*Util*/
  function isNullUndefEmpty(obj) {
    return obj === null || obj === undefined || obj === "";
  }
  function getCarretSelection(
    prevValidWin,
    lastValidWin,
    inputType,
    prevCarret
  ) {
    if (prevValidWin === lastValidWin) {
      if (inputType.includes("delete")) {
        return prevCarret;
      }
      return prevCarret + 1;
    }
    const prevValidWinString = prevValidWin.toString();
    const lastValidWinString = lastValidWin.toString();
    let i = 0;
    while (prevValidWinString[i] === lastValidWinString[i]) i++;

    if (inputType.includes("delete")) {
      return i;
    }
    return i + 1;
  }
  function setCarret(htmlElement, pos) {
    htmlElement.setAttribute("type", "tel");
    userSelected = false;
    htmlElement.setSelectionRange(pos, pos); // This will fire handleSelect
    htmlElement.setAttribute("type", "number");
  }
</script>

<input
  type="number"
  on:paste|preventDefault
  on:select={handleSelect}
  on:input={handleInput}
  value={new Big('0').toFixed(fixedDigits).toString()} />
