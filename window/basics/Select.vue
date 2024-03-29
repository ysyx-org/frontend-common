<template>
    <div options>
        <Responsive
            v-for="(option, i) in options"
            :key="i"
            option
            :selected="!!option?.value"
            :disabled="!!option?.disabled"
            @click="option.value = !option.value"
        >
            <div checkbox>
                <svg viewBox="0 0 20 20">
                    <g check-mark>
                        <path
                            d="M6.5,17a1.5,1.5,0,0,1-1.06-.44l-5-5A1.5,1.5,0,0,1,2.56,9.44l3.89,3.89,8.94-9.84a1.5,1.5,0,1,1,2.22,2l-10,11A1.51,1.51,0,0,1,6.54,17Z"
                        />
                    </g>
                </svg>
            </div>
            <div option-name>
                <locale-name :name="option.name" />
            </div>
            <div
                v-if="showKey"
                option-key
            >
                {{ option.key }}
            </div>
        </Responsive>
    </div>
    <div btn-group>
        <btn
            type="solid green"
            @click="submit"
        >
            confirm
        </btn>
        <btn
            type="solid gray"
            @click="reset"
        >
            reset
        </btn>
    </div>
</template>

<script>
import { defineComponent, ref } from 'vue';
export default defineComponent({
    setup(options = [], showKey = false, hook = () => {}) {
        const mapping = ([key, name = key, value = false, disabled = false]) => ({
            key, name, value, initial: value, disabled
        });
        let promise;
        if (options instanceof Promise) {
            promise = options;
            options = ref([]);
            promise.then(arr => options.push(...arr.map(mapping)));
        } else options = ref(options.map(mapping));
        return { options, promise, showKey, hook };
    },
    computed: {
        result() {
            return this.options
                .filter(({ value }) => value)
                .map(({ key }) => key);
        }
    },
    mounted() {
        if (this.promise instanceof Promise) this.LOAD(this.promise);
    },
    methods: {
        reset() {
            for (const option of this.options) {
                option.value = option.initial;
            }
        },
        async submit() {
            const { result } = this;
            await this.hook(result);
            this.RETURN(result);
        }
    }
});
</script>


<style lang="scss" scoped>
[options] {
  user-select: none;
  min-width: 50vw;
  [option] {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    height: 3.6em;
    padding: 1em;
    align-items: center;
    border-bottom: 1px solid var(--cb-gray-light);
    &[disabled="true"] {
      pointer-events: none;
      filter: saturate(0);
      opacity: 0.5;
    }
    &[selected="true"] [checkbox] {
      background-color: var(--ct-green);
      border-color: var(--ct-green);
      svg {
        opacity: 1;
      }
    }
    [checkbox] {
      width: 1.2em;
      height: 1.2em;
      border-radius: 0.3em;
      border: 1px solid var(--ct-gray-dark);
      &,
      * {
        transition: 0.1s;
        transition-delay: 0;
      }
      svg {
        transform: scale(0.85);
        opacity: 0;
        [check-mark] {
          fill: var(--cf);
        }
      }
    }
    [option-name] {
      flex-grow: 1;
      line-height: 100%;
      padding: 0.3em 1em;
      color: var(--ct-gray-dark);
    }
    [option-key] {
      font-size: 0.8em;
      line-height: 100%;
      padding: 0.5em 0;
      color: var(--ct-gray);
    }
  }
}
</style>
