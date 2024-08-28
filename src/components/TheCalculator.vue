<script setup lang="ts">
import { ref } from 'vue'

import { Button } from '@/components/ui/button'
import {
    Select,
    SelectContent,
    SelectGroup,
    SelectItem,
    SelectLabel,
    SelectTrigger,
    SelectValue
} from '@/components/ui/select'

const PRIMARY_PARSE = /[+|-]?[\d|*|/|.]+(?!=[+|-])/g
const ONLY_NUMBERS = /[\d|.]+/g
const ONLY_OPERATORS = /[*|/]+/g

type Operator = '+' | '-' | '*' | '/'

const operators = {
    add: '+',
    sub: '-',
    mul: '*',
    div: '/'
} as const

const operatorsArr = Object.values(operators)

const input = ref('')

const updateInput = (newValue: string) => (input.value = newValue)
const resetInput = () => updateInput('')

const getLastChar = (str: string) => str.slice(-1)
const isNumber = (str: string): boolean => Number.isInteger(parseInt(str, 10))
const formatInput = (symbol: string) => {
    const { value } = input
    const lastChar = getLastChar(value)

    const symbolIsOperator = operatorsArr.includes(symbol as any)
    const operatorInPrevValueAtTheEnd = operatorsArr.includes(lastChar as any)
    const shouldReplaceLastOperator = operatorInPrevValueAtTheEnd && symbolIsOperator

    if (isNumber(symbol) || isNumber(lastChar)) {
        input.value += symbol
    } else if (shouldReplaceLastOperator) {
        input.value = value.slice(0, -1) + symbol
    }
}
const addSymbol = (symbol: string | number) => {
    formatInput(symbol.toString())
    updateInput(input.value)
}

const isOperator = (char: string): boolean => operatorsArr.some((c) => char === c)

const removeOperatorTailIfItExists = (input: string) => {
    if (isOperator(input.slice(-1))) return input.substring(0, input.length - 1)
    return input
}

const getSign = (input: string): Operator => {
    const firstChar = input.charAt(0)

    return Number.isInteger(parseInt(firstChar)) ? '+' : (firstChar as Operator)
}

const getNumbers = (input: string) => input.match(ONLY_NUMBERS) as string[]
const getOperators = (input: string) => input.match(ONLY_OPERATORS) as any as Operator[]

const calc = (a: number, b: number, op: Operator) => {
    if (op === '+') return a + b
    if (op === '-') return a - b
    if (op === '*') return a * b
    if (op === '/') return a / b

    return 0
}

const compute = () => {
    removeOperatorTailIfItExists(input.value)

    const primary = input.value.match(PRIMARY_PARSE)
    const secondary = primary!.map((input) => ({
        sign: getSign(input),
        numbers: getNumbers(input),
        operators: getOperators(input)
    }))
    const tertiary = secondary.map(({ sign, numbers, operators }) => {
        const resultWithSign = (result: number) => (sign === '+' ? result : -result)

        if (numbers?.length === 1) return resultWithSign(parseFloat(numbers[0]))
        if (numbers == null || operators == null) return 0

        const result = Math.abs(
            (numbers as any[]).reduce((prev, curr, i) => {
                const operator = operators[i - 1]
                const a = parseFloat(prev)
                const b = parseFloat(curr)

                return calc(a, b, operator as Operator)
            }) as any
        )

        return resultWithSign(result)
    })

    input.value = tertiary.reduce((prev, curr) => prev + curr).toString()
}

resetInput()
</script>

<template>
    <div class="size-[22rem] p-4 grid grid-cols-3 gap-2 rounded-xl border select-none">
        <Button class="h-full" variant="destructive" @click="resetInput"> AC </Button>
        <code
            class="h-full px-4 flex items-center justify-end text-2xl font-medium col-span-2 border rounded-md overflow-hidden text-nowrap tracking-wide"
        >
            {{ input || '0' }}
        </code>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(7)">7</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(8)">8</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(9)">9</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(4)">4</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(5)">5</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(6)">6</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(1)">1</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(2)">2</Button>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(3)">3</Button>
        <Select @update:model-value="addSymbol">
            <SelectTrigger class="size-full">
                <SelectValue placeholder="Operator" />
            </SelectTrigger>
            <SelectContent>
                <SelectGroup>
                    <SelectLabel>Operation</SelectLabel>
                    <template v-for="value in operators" :key="value">
                        <SelectItem :value>
                            {{ value }}
                        </SelectItem>
                    </template>
                </SelectGroup>
            </SelectContent>
        </Select>
        <Button class="size-full" variant="secondary" @click="() => addSymbol(0)">0</Button>
        <Button class="size-full" variant="default" @click="compute">=</Button>
    </div>
</template>
