
<script>
    import { AnimatedCounter } from '@benzara/svelte-animated-counter';

    var roundNumber = 0
    var roundState = "Oczekiwanie na rozpoczęcie."

    var colors = {
        "blue": "#07227d",
        "green": "#204c28",
        "yellow": "#a88316",
    }

    var roundScores = {
        "blue": 0,
        "green": 0,
        "yellow": 0,
    }

    var money = {
        "blue": 5000,
        "green": 5000,
        "yellow": 5000,
    }

    var moneyStart = {
        "blue": 5000,
        "green": 5000,
        "yellow": 5000,
    }
    
    var auction = {
        "blue": 0,
        "green": 0,
        "yellow": 0,
    }
    $: money.blue = moneyStart.blue - auction.blue;
    $: money.green = moneyStart.green - auction.green;
    $: money.yellow = moneyStart.yellow - auction.yellow;
    $: auctionSum = lastAuctionSum + auction.blue + auction.green + auction.yellow + bonusAuction
    $: latestValue = Math.max(auction.blue, auction.green, auction.yellow)
    $: currentColor = (auction.yellow <= 200 && auction.yellow <= 200 && auction.green <= 200) ? "rgb(32, 32, 32)" : colors[Object.keys(auction).reduce(function(a, b){ return auction[a] > auction[b] ? a : b })]

    let currentColor = "black"
    let latestValue = 200
    let auctionSum = 0
    let lastAuctionSum = 0
    let answeringTeam = ""
    let bonusAuction = 0


    function startAuction() {
        startCounter = false
        roundNumber += 1
        roundState = "Aukcja trwa. Oczekiwanie na koniec licytacji."
        
        auction.blue = (moneyStart.blue > 200) ? auction.blue += 200 : auction.blue = 0
        auction.green = (moneyStart.green > 200) ? auction.green += 200 : auction.green = 0
        auction.yellow = (moneyStart.yellow > 200) ? auction.yellow += 200 : auction.yellow = 0
    }

    function endAuction() {
        answeringTeam = Object.keys(auction).reduce(function(a, b){ return auction[a] > auction[b] ? a : b })
        roundState = "Pytanie kupione przez zespół " + answeringTeam + ". Oczekiwanie na odpowiedź."
        roundScores = {
            "blue": money.blue,
            "green": money.green,
            "yellow": money.yellow,
        }

        lastAuctionSum = auctionSum
        bonusAuction = 0

        auction.blue = 0
        auction.green = 0
        auction.yellow = 0

        moneyStart = roundScores
        
    }

    function answerQuestion(correct) {        
        if (correct) {
            roundState = "Prawidłowa odpowiedź. Zespół " + answeringTeam + " zdobywa " + lastAuctionSum + ". Oczekiwanie na start licytacji."
            money[answeringTeam] += lastAuctionSum
            auctionSumForAnimation = lastAuctionSum
            lastAuctionSum = 0
            counterDirection = "up"
        } else {
            roundState = "Nieprawidłowa odpowiedź. Do puli przechodzi " + lastAuctionSum + ". Oczekiwanie na start licytacji."
            auctionSumForAnimation = lastAuctionSum
            counterDirection = "down"
        }
        startCounter = true

        roundScores = {
            "blue": money.blue,
            "green": money.green,
            "yellow": money.yellow,
        }
        moneyStart = roundScores
    }

    const range = (start, stop, step) =>
        Array.from({ length: (stop - start) / step + 1 }, (_, i) => start + i * step);

    let auctionSumForAnimation = 0
    let startCounter = false
    let counterDirection = ""

</script>
<div style="background-color: gray; width: 100vw; height: 100vh">

<div class="layout" style="display: flex; flex-direction: column;">
    <p style="display: flex; justify-content: space-evenly; align-items: center; background-color: black; width: 750px; height: 25px; font-size: 20px;">LICYTACJA</p>
    <div style="display: flex; justify-content: space-evenly; align-items: center; width: 750px; font-size: 50px;">
        <p class="glow" style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: #07227d; height: 75px">{auction.blue}</p>
        <p class="glow" style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: #204c28; height: 75px">{auction.green}</p>
        <p class="glow" style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: #a88316; height: 75px">{auction.yellow}</p>
    </div>
    <div style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 20px;">
        <p style="display: flex; justify-content: center; flex: 0 1 25%; background-color: black; height: 25px;">STAN KONTA</p>
        <p style="display: flex; justify-content: center; flex: 0 1 25%; background-color: black; height: 25px;">STAN KONTA</p>
        <p style="display: flex; justify-content: center; flex: 0 1 25%; background-color: black; height: 25px;">STAN KONTA</p>
        <p style="display: flex; justify-content: center; flex: 0 1 25%; background-color: black; height: 25px;">PULA</p>
    </div>
    <div style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 50px;">
        <p class="glow-dark" style="display: flex; justify-content: center; align-items: center; flex: 0 1 25%; background-color: #07227d; height: 75px">{money.blue}</p>
        <p class="glow-dark" style="display: flex; justify-content: center; align-items: center; flex: 0 1 25%; background-color: #204c28; height: 75px">{money.green}</p>
        <p class="glow-dark" style="display: flex; justify-content: center; align-items: center; flex: 0 1 25%; background-color: #a88316; height: 75px">{money.yellow}</p>
        <p class="glow-dark" style="display: flex; justify-content: center; align-items: center; flex: 0 1 25%; background-color: rgb(58, 58, 58); height: 75px">{auctionSum}</p>
    </div>
</div>


<div class="layout" style="display: flex; justify-content: center; align-items: center; flex: 0 1 50%; background-color: {currentColor}; width: 1000px; height: 700px; font-size: 200px;">
    {#if startCounter}
        <div style="display: flex; width: 800px; justify-content: center; align-items: center;">
            <AnimatedCounter
                class="counter"
                values={range(0, auctionSumForAnimation, 100)}
                interval=50
                startImmediately={false}
                direction={counterDirection}
                loop={false}
                ease="cubic-bezier(0.25, 0.1, 0.25, 1)" 
            />
        </div>
    {:else}
        {auctionSum}
    {/if}
    
</div>


<div>
    <div style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; height: 50px; font-size: 20px; background-color: rgb(58, 58, 58);">
        {#if roundNumber > 0}
            <p>Runda {roundNumber}. {roundState}</p>
        {:else}
            <p>{roundState}</p>
        {/if}
    </div>

    <form style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 80px;">
        <button
            on:click={() => startAuction()}
            style="display: flex; justify-content: center; align-items: center; flex: 0 0 75%; background-color: rgb(180,180,180); height: 80px; font-size: 20px;">
            START LICYTACJI
        </button>
        <input 
            type="number" 
            step=500
            min=0
            bind:value={bonusAuction}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 25%; background-color: #f7d93e; height: 75px; font-size: 20px;"/>
    </form>
</div>

<div>
    <div style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 20px;">
        <p style="display: flex; justify-content: center; flex: 0 1 33.3%; background-color: black; height: 25px;">NOWA WARTOŚĆ</p>
        <p style="display: flex; justify-content: center; flex: 0 1 33.3%; background-color: black; height: 25px;">NOWA WARTOŚĆ</p>
        <p style="display: flex; justify-content: center; flex: 0 1 33.3%; background-color: black; height: 25px;">NOWA WARTOŚĆ</p>
    </div>
    <form style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 50px;">
        <input 
            type="number" 
            step="100"
            min=0
            max="{moneyStart.blue}"
            bind:value={auction.blue}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: #07227d; height: 75px; font-size: 20px;"/>
        <input 
            type="number" 
            step="100"
            min=0
            max="{moneyStart.green}"
            bind:value={auction.green}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: #204c28; height: 75px; font-size: 20px;"/>
        <input 
            type="number" 
            step="100"
            min=0
            max="{moneyStart.yellow}"
            bind:value={auction.yellow}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: #a88316; height: 75px; font-size: 20px;"/>
      </form>
      <form style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 50px;">
        <button
            on:click={() => {auction.blue = moneyStart.blue}}
            on:click={() => {endAuction()}}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: rgb(180,180,180); height: 40px; font-size: 20px;">
            VA BANQUE
        </button>
        <button
            on:click={() => {auction.green = moneyStart.green}}
            on:click={() => {endAuction()}}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: rgb(180,180,180); height: 40px; font-size: 20px;">
            VA BANQUE
        </button>
        <button
            on:click={() => {auction.yellow = moneyStart.yellow}}
            on:click={() => {endAuction()}}
            style="display: flex; justify-content: center; align-items: center; flex: 0 1 33.3%; background-color: rgb(180,180,180); height: 40px; font-size: 20px;">
            VA BANQUE
        </button>
</div>

<div>
    <form style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 80px;">
        <button
        on:click={() => endAuction()}
        style="display: flex; justify-content: center; align-items: center; flex: 0 0 100%; background-color: rgb(180,180,180); height: 80px; font-size: 20px;">
        KONIEC LICYTACJI
        </button>
    </form>
</div>

<div>
    <form style="display: flex; justify-content: space-evenly; align-items: center; width: 1000px; font-size: 80px;">
        <button
        on:click={() => answerQuestion(false)}
        style="display: flex; justify-content: center; align-items: center; flex: 0 0 50%; background-color: #d54949; height: 80px; font-size: 20px;">
        NIEPOPRAWNIE
        </button>
        <button
        on:click={() => answerQuestion(true)}
        style="display: flex; justify-content: center; align-items: center; flex: 0 0 50%; background-color: #57a430; height: 80px; font-size: 20px;">
        POPRAWNIE
        </button>
    </form>
</div>

</div>

<style>
    .layout {
        font-family: 'LED Dot-Matrix';
    }

    .glow {
        text-shadow: 0 0 1px #ffffff, 0 0 2px #ffffff, 0 0 50px #ffffff, 0 0 80px #ffffff;
    }

    .glow-dark {
        text-shadow: 0 0 1px #000, 0 0 10px #000, 0 0 50px #000, 0 0 80px #000;
    }

    .counter {
        background-color: aqua;
    }

</style>
