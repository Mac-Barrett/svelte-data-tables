<script lang="ts">
    import jQuery from 'jquery';
    import DataTables from 'datatables.net';
    import type { Config, Api as DataTablesAPI } from 'datatables.net';
    import { onDestroy, onMount, tick } from 'svelte';
    
    /** 
     * DataTables config object. Use to set DataTables settings. 
     * See the [DataTables.net](https://datatables.net) documentation for more.
     */
    export let config: Config | undefined

    /**
     * Filter callback function. The DataTables API will automatically use
     * this to determine which rows show up in your table. Setting this callback
     * to a new function will automatically reinstantiate the API and filter the
     * data according to the new filter callback.
     * 
     * @returns {boolean} true to show the row, false to hide it.
     */
     export var filterCallback: (settings: any, data: string[], row: number, rawInput: any[], searchCounter: number) => boolean = 
        (settings, data, row, rawInput, searchCounter): boolean => { return true; }

    $: {
        if (mounted) {
            //@ts-ignore Filter bullshit and other archaic magic
            while(jQuery.fn.dataTable().DataTable.ext.search.length > 0) {
                //@ts-ignore
                jQuery.fn.dataTable().DataTable.ext.search.pop();
            }
            //@ts-ignore
            jQuery.fn.dataTable().DataTable.ext.search.push((settings, data, row, rawInput, searchCounter) => {
                return filterCallback(settings, data, row, rawInput, searchCounter);
            });
            reinitializeAPI();
        }
    }

    let tableElement: HTMLTableElement | undefined
    let dataTableAPI: DataTablesAPI<any> | undefined

    let mounted = false;
    onMount(async () => {
        await tick();

        if (tableElement !== undefined) {
            dataTableAPI = new DataTables('#table', config);
        }
        else console.error('DataTable Element is undefined.')
        mounted = true;
    });
    onDestroy(() => {
        dataTableAPI?.destroy();
    })

    /**
     * Can be called to return the instance of the DataTablesAPI for the table.
     * See the [DataTables.net](https://datatables.net) documentation for more 
     * information about the API
     */
    export const getAPI = () => { return dataTableAPI; }

    /** 
     * Call this to reinitialize the datatables API so that it will use new data
     * that has been inserted into the table. You may also choose to insert a 
     * callback function that will update the tables data in the function.
     */
    export const reinitializeAPI = async (newDataCallback: (() => Promise<any>) | undefined = undefined) => {
        await tick();
        if (tableElement !== undefined) {
            dataTableAPI?.destroy();

            if (newDataCallback !== undefined) {
                await newDataCallback();
            }
            await tick();

            dataTableAPI = new DataTables('#table', config);
        }
        else console.error('DataTable Element is undefined.')
    }
</script>

<table id="table" bind:this={tableElement}>
    <slot name="thead"/>
    <slot name="tbody"/>
</table>

<style>
    @import '/node_modules/datatables.net-dt/css/jquery.dataTables.css';
    @import '/node_modules/datatables.net-dt/css/jquery.dataTables.min.css';

    table {
        width: 100%;
    }
</style>

<!--
@component
Svelte wrapper component for the DataTables.net npm package.
See the [DataTables.net](https://datatables.net) documentation for more information about the API

### Usage:
```tsx
<SvelteDataTable bind:this={myDataTable}
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
```
### Properties:
Data tables config object. Used to set datatables properties.
See [DataTables.net](https://datatables.net) for more information.
```ts
export let config: Config | undefined
```
Filter callback function. The DataTables API will automatically use
this to determine which rows show up in your table.
```ts
export var filterCallback: (settings: any, data: string[], row: number, rawInput: any[], searchCounter: number) => boolean
```
### Methods:
Can be called to return the instance of the DataTablesAPI for the table.
See the [DataTables.net](https://datatables.net) documentation for more
information about the API

```ts
export const getAPI = () => { return dataTableAPI; }
```

Call this to reinitialize the datatables API so that it will use new data
that has been inserted into the table.
```ts
export const reinitializeAPI = async (newDataCallback: (() => Promise<any>) | undefined = undefined) => {
    await tick();
    if (tableElement !== undefined) {
        dataTableAPI?.destroy();

        if (newDataCallback !== undefined) {
            await newDataCallback();
        }
        await tick();

        dataTableAPI = new DataTables('#table', config);
    }
    else console.error('DataTable Element is undefined.')
}
```


-->