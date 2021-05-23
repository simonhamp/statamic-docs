---
id: faca60ca-be69-4b1b-9722-20ad13bd6228
title: 'Data Lists'
updated_by: 5880249a-a0af-4e86-8384-77051b66ce61
updated_at: 1621769329
---
Data Lists are used extensively in the Statamic CP. When building your own extensions, making use of Data Lists will help keep your interface consistent with the rest of the CP with predictable behaviour for your users.

<figure>
    <img src="/img/data-lists.png" alt="An example of a Data List in use" class="p-4 bg-white" width="500">
    <figcaption>The Taxonomies Data List in use.</figcaption>
</figure>

Data Lists are powerful and flexible components that allow you to present multiple rows of information with columns, column customization, pagination, sorting & filtering, per-line actions and even bulk actions. Making use of them in your own components will bring powerful features to your Add-ons.

In this guide, we'll look at Data Lists in more detail.

## The `<data-list>` component

At the root of all Data Lists is the the `<data-list>` component. By itself, `<data-list>` doesn't do much, but it's crucial to understand as it's the foundations of all of the Data List functionality.

Let's look at a simplified version of Taxonomies Data List to see how things work:

```html
  <data-list
      :rows="items"
      :columns="columns"
      :sort="false"
      :sort-column="sortColumn"
      :sort-direction="sortDirection"
      @visible-columns-updated="visibleColumns = $event"
  >
    ...
  </data-list>
```

We can see a number of props being passed into the component:

1. `rows` _Array\<Object\>_ **required**  
    Pretty self-explanatory, these are the rows of data that you want to present. This should be an array of objects. For data to be shown, the key names in each object - each 'row' - need to match the names given in `columns`.
1. `columns` _Array\<Object\>_ **required**  
    The columns that you want to make available in the Data List. We'll look at how to structure these shortly.
1. ``
```js
<script>
    export default {
        data() {
            return {
                rows: this.data,
                columns: [
                    {
                        'field': 'change',
                        'label': '',
                        'width': '50',
                    },
                    {
                        'field': 'relative_path',
                        'label': 'Path',
                    },
                    {
                        'field': 'last_modified',
                        'label': 'Last modified',
                        'width': '200',
                    }
                ],
            }
        },

        computed: {
        },

        watch: {
            data(newValue, oldValue) {
                this.rows = newValue;
            }
        },

        created() {
        },

        methods: {
            beginAction() {
                let rows = Object.values(this.rows);

                const selections = Object.values(this.$refs.list.sharedState.selections);

                const selected = rows.filter(row => {
                    return selections.includes(row.id);
                });

                this.rows = rows.filter(row => {
                    return ! selections.includes(row.id);
                });

                Object.values(selected).forEach(row => {
                    this.$root.$refs.status.staged.push(row);
                });
            },

            finishAction(success) {
                if (success) {
                    this.$toast.success('Bulk action completed successfully');
                }
                this.refresh();
            },
        },
	}
</script>
```