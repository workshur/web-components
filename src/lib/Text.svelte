<svelte:options tag="cml-text" />

<script type="ts">
  import * as yup from "yup";
  import ShortUniqueId from "short-unique-id";

  const generateUniqId = new ShortUniqueId({ length: 6 });

  const uniqFieldId = generateUniqId();
  export let label: string;
  export let name: string;
  export let instructions: string | undefined;
  export let value: string | undefined;
  export let validates: string | undefined;

  let schema = yup.string();
  let required = false;

  validates?.split(" ").forEach((option) => {
    const [validator, value] = option.split(":");

    switch (validator) {
      case "required":
        schema = schema.required();
        required = true;
        break;
      case "maxLength":
        schema = schema.max(Number(value));
        break;
      case "minLength":
        schema = schema.min(Number(value));
        break;
      default:
      // nothing;
    }
  });
</script>

<fieldset>
  <label for={uniqFieldId}>
    {label}
    {#if required}
      <span class="required">(required)</span>
    {/if}
  </label>
  <input id={uniqFieldId} {name} type="text" bind:value />
  {#if instructions}
    <p>{instructions}</p>
  {/if}
  {#await schema.validate(value)}
    <p class="valid" />
  {:catch error}
    <p class="invalid" style="color:red">{error.errors[0]}</p>
  {/await}
</fieldset>

<style>
  fieldset {
    border: none;
    display: grid;
    grid-column: 1;
  }
  label {
    display: block;
    font-weight: 700;
    color: #4c4c4e;
    padding-bottom: 2px;
    font-size: 14pt;
    margin-top: 10px;
    margin-bottom: 3px;
    line-height: 105%;
    cursor: pointer;
  }
  .required {
    padding-left: 5px;
    font-weight: bold;
    color: #999;
  }
</style>
