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
  import { onMount } from "svelte";
  let input;
  import { EMPTY, fromEvent, of } from "rxjs";
  import {
    catchError,
    filter,
    map,
    switchMap,
    tap,
    timeoutWith,
  } from "rxjs/operators";
  // import Big from "big.js"; // new Big("0").toFixed(fixedDigits) // Not $reactive updated only when new valid input new Big('0').toFixed(fixedDigits).toString()
  //export const fixedDigits = 2;
  let isGecko;
  let focus$;
  let click$;
  let keyUp$;
  let blur$;
  let landingClick$;
  let landingTab$;

  onMount(() => {
    // * Determine if we are in PureGeko (Firefox) or like Geko (Chrome,Safari,Edge,Opera)
    isGecko = isBrowserGecko();
    // * This grant that input var is a defined htmlInput
    // * $treams definition
    focus$ = fromEvent(input, "focus");
    click$ = fromEvent(input, "click");
    keyUp$ = fromEvent(input, "keyup");
    blur$ = fromEvent(blur, "blur");
    // * Landing position by Click (Focus + click)
    landingClick$ = focus$.pipe(
      switchMap((e) => {
        // cancel previous active subscription and subscribe to the returned observable
        return click$.pipe(timeoutWith(100, EMPTY)); // will complete if timeOut.
      })
    );
    // * Landing position by Tabulation (Focus + KeyUp key Tab)
    landingTab$ = focus$.pipe(
      switchMap((e) => {
        // cancel previous active subscription and subscribe to the returned observable
        return keyUp$.pipe(
          filter((e) => e.key === "Tab"), // Only fire when the focus is made by Tab
          timeoutWith(100, EMPTY) // will complete if timeOut.
        );
      })
    );

    // * Current Carret
    /*tap(
          (a) => console.log("a", a),
          (b) => console.log("b", b),
          () => console.log("c")
        )*/
  });
  // Functional
  function isBrowserGecko() {
    // ! This is why selection.modify does not work on Mozilla Input Number
    // * Mozilla does not fulfill window.selection on focus Input type Number
    // * Returning this if nothing else is selected Mozila: {type:"None", rangeCount="0"} selection.toString() = ""
    // * Or Returning another carret if user has the carret previusly on other stuff

    //* Chrome Safari Edge are now WebKit like Gecko
    if (navigator.userAgent.toLowerCase().indexOf("like gecko") > -1) {
      return false;
    }
    //* Firefox includes Gecko/geckotrail
    if (navigator.userAgent.toLowerCase().indexOf("gecko/")) {
      return true;
    }
  }
</script>

<input
  type="number"
  on:paste|preventDefault
  value={(0).toFixed(2)}
  bind:this={input} />
