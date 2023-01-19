# Svelte DataTable Component

## About:
Svelte component that wraps around the DataTable api. See [datatables.net](https://datatables.net)

### Getting Started:
Install:
```sh
npm i @mac-barrett/svelte-data-table
```
Import: 
```ts
import { SvelteDataTable } from '@mac-barrett/svelte-data-table'
```
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
