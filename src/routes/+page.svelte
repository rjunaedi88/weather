<script lang="ts">
    import { onMount } from "svelte";
    import Select from "svelte-select";
    let provinces:proviceType[] = [];
    let weatherRes:WeatherResponse;
    let showCard:boolean = false;
    let icon:string;
    let weatherResult:string;

    type proviceType = {
        value:number,
        label:string
    }

    interface WeatherResponse {
        location: {
            name: string;
            region: string;
            country: string;
            tz_id: string;
        };
        current: {
            temp_c: number;
            temp_f: number;
            condition: {
                text: string;
                icon: string;
            };
        };
    }

    async function weather() {
        let city = provinces.values
        const apiKey = "13619b0338644d238d685442252611";
        const res = await fetch(`https://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${city.label}`);
        weatherRes = await res.json();
        console.log(weatherRes);
        showCard = true;
        icon = weatherRes.current.condition.icon;
        weatherResult = weatherRes.current.condition.text;
        
    }


    onMount(async () => {
        const res = await fetch("https://www.emsifa.com/api-wilayah-indonesia/api/provinces.json");
        const response: { id: number; name: string }[] = await res.json();

        provinces = response.map((item) => ({
            value: item.id,
            label: item.name
        }));
    });

</script>

<h1 class="text-center text-primary">Cuaca Hari Ini</h1>

<div class="container">
    <form class="row g-3">
        <div>
            <label for="inputPassword2" class="visually-hidden">Password</label>
            <Select 
                items={provinces}
                bind:value={provinces.values}
            />
            
        </div>
        <div class="col-auto">
            <button type="submit" class="btn btn-primary mb-3" 
                on:click={weather} 
            >
                Confirm identity
            </button>
        </div>
    </form>

    {#if showCard }
    <div class="card" style="width: 18rem;">
        <img src={icon} class="card-img-top">
        <div class="card-body">
            <h5 class="card-title">{weatherResult}</h5>
            <p class="card-text">name : {weatherRes.location.name} / {weatherRes.location.country}</p>
            <p class="card-text">region : {weatherRes.location.region}</p>
            <p class="card-text">timezone : {weatherRes.location.tz_id}</p>
        </div>
    </div>
    {/if}

</div>
