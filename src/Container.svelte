<script>
  import Ammount from "./Ammount.svelte";
  import Summary from "./Summary.svelte";
  import PlaceBet from "./PlaceBet.svelte";
</script>

<!-- Container should render the component that we are about to descrive -->
<!-- The component should have 2 inputs toRisk toWin -->
<!-- By default both inputs will be editable but The component some times will have ToRisk Enabled and some other ToWin -->
<!-- Modify one of them and the inputs should be sanitized, formated, and reCalculated by multyplyng *10 the toRisk -->
<!-- When a certain event comes from Parent both input will have to update the values, if the values are different we want a One time border color Red-->
<!-- Req - Sumary
    In
    onInit()
        ToRiskEnabled:bool, ToWinEnabled:bool
    onChangeOdds()
        (newValues{risk:number, win:number} | newOdds:number)
    --------------------
    ||    Inner Presentation string State            ||
    --------------------
    Out
    onUserInput()
        newNumberValues{risk:text, win:text}    
 -->
<!-- Basic Dev Env: fakeProducerButtons, multi instance, consumer(summary,place) -->
<button>UpdateOdds of 1st bet</button>
<br />
<Ammount riskAble={false} winAble={true} decimalChar="," />
<br />
<Ammount />
<hr />
<Summary />
<PlaceBet />
