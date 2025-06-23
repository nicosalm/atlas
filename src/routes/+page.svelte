<script>
import Map from '$lib/Map.svelte';
let map;
</script>

<main>
    <Map
        bind:this={map}
        imagePath="/fantasy-world.png"
    />

    <div class="controls">
        <button onclick={() => map.flyTo([-50, 30], 3)}>
            Fly to North Sea
        </button>
        <button onclick={() => map.flyTo([20, -10], 4)}>
            Fly to Exalted Plains
        </button>
        <button onclick={() => map.flyTo([0, 0], 0)}>
            Return to Overview
        </button>
    </div>
</main>

<style>
main {
    padding: 1rem;
}

.controls {
    display: flex;
    gap: 0.5rem;
    margin-top: 1rem;
    justify-content: center;
}

button {
    padding: 0.5rem 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    background: white;
    cursor: pointer;
}

button:hover {
    background: #f5f5f5;
}
</style>
