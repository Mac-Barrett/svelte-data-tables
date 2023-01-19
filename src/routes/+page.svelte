<script>
    import { SvelteDataTable } from '$lib';
    import { onMount, tick } from 'svelte';

    /**
     * @type {SvelteDataTable}
     */
    let dt_table

    let headers = ["foo", "bar", "baz"]
    let rows = [
        [1, 2, 3], 
        [4, 5, 6],
        [3, 4, 5]
    ];

    let show = false;
</script>


<SvelteDataTable bind:this={dt_table}
    config={{
        paging: false,
        autoWidth: false
    }}
>
    <thead slot="thead">
        <tr>
            {#each headers as header}
                <td>{header}</td>
            {/each}
        </tr>
    </thead>

    <tbody slot="tbody">
        {#each rows as row}
            <tr>
                {#each row as col}
                    <td>{col}</td>
                {/each}
            </tr>
        {/each}
    </tbody>
</SvelteDataTable>

<style>

</style>