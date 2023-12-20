<script>
  import { Heading, P, Button } from 'flowbite-svelte';
  
  let operator = ''
  let calcul = ''
  let number = '0'
  let total = 0
  let hasNumberChange = true
  
  const calc = {
    '+': () => parseFloat(number) + total,
    '-': () => total === 0 ? parseFloat(number) : total - parseFloat(number),
    '*': () => total === 0 ? parseFloat(number) : parseFloat(number) * total,
    '/': () => total === 0 ? parseFloat(number) : total / parseFloat(number)
  }

  function operation(o) {

    if (!hasNumberChange) {
      operator = o
      calcul = `${total} ${operator}`
      return
    }

    if (!operator) {
      operator = o
    }

    total = calc[operator]()

    operator = o
    hasNumberChange = false
    calcul = `${total} ${operator}`
  }

  function addComma() {
    const hasComma = number.includes('.')
    if (hasComma) return
    number = `${number}.`
  }

  function addNumber(n) {

    const hasEqual = calcul.includes('=')
    if (hasEqual) {
      clear()
    }

    if (operator && !hasNumberChange) {
      number = '0'
      hasNumberChange = true
    }

    const hasComma = number.includes('.')
    if (hasComma) {
      const decimal = number.replace(/^.*\./, '')
      if (decimal.length === 10) return
    }

    if (number.length === 15) return

    number = number === '0' ? n : `${number}${n}`
  }

  function remove() {
    number = number.length === 1 ? '0' : number.slice(0, -1)
  }

  function clear() {
    total = 0
    number = '0'
    calcul = ''
    operator = ''
    hasNumberChange = true
  }

  function equal() {
    if (!total) return
    hasNumberChange = false
    calcul = `${total} ${operator} ${number} =`
    total = calc[operator]()
  }

  function percentage() {

    hasNumberChange = true

    const hasEqual = calcul.includes('=')
    if (hasEqual) {
      number = total.toString()
      operator = ''
      calcul = ''
      total = 0
    }

    switch (operator) {
      case '+':
      case '-':
        number =  `${total * (parseFloat(number) / 100)}`
        break;
      default:
        number = `${parseFloat(number) / 100}`
        break;
    }
  }

  function polarity() {

    const hasEqual = calcul.includes('=')
    if (hasEqual) {
      number = total.toString()
      calcul = ''
      total = 0
    }

    number = Math.sign(parseFloat(number)) ? `${-number}` : `${+number}`
    hasNumberChange = true
  }

  function onKeydown(e) {
    switch (e.key) {
      case '0':
      case '1':
      case '2':
      case '3':
      case '4':
      case '5':
      case '6':
      case '7':
      case '8':
      case '9':
          addNumber(e.key)
        break
      case '+':
      case '-':
      case '*':
      case '/':
        operation(e.key)
        break
      case '=':
      case 'Enter':
        equal()
        break
      case 'Escape':
        clear()
        break
      case ',':
      case '.':
        addComma()
        break
      case '%':
        percentage()
        break
    }
  }
</script>

<Heading tag="h1" class="mb-5" customSize="text-4xl font-extrabold  md:text-5xl lg:text-6xl">Calculator</Heading>

<svelte:body on:keydown={onKeydown} />

<main>
  <P align="center">{calcul}</P>

  <div class="total">
    <P class="mb-5" size="4xl" weight="bold" align="center">{hasNumberChange ? number : total}</P>
  </div>

  <div class="calculator">
    <Button color="light" on:click={percentage}>%</Button>
    <Button color="light" on:click={clear}>C</Button>
    <Button color="light" on:click={remove}>{'<-'}</Button>
    <Button color="light" on:click={() => operation('/')}>/</Button>

    <Button color="light" on:click={() => addNumber('7')}>7</Button>
    <Button color="light" on:click={() => addNumber('8')}>8</Button>
    <Button color="light" on:click={() => addNumber('9')}>9</Button>
    <Button color="light" on:click={() => operation('*')}>*</Button>

    <Button color="light" on:click={() => addNumber('4')}>4</Button>
    <Button color="light" on:click={() => addNumber('5')}>5</Button>
    <Button color="light" on:click={() => addNumber('6')}>6</Button>
    <Button color="light" on:click={() => operation('-')}>-</Button>

    <Button color="light" on:click={() => addNumber('1')}>1</Button>
    <Button color="light" on:click={() => addNumber('2')}>2</Button>
    <Button color="light" on:click={() => addNumber('3')}>3</Button>
    <Button color="light" on:click={() => operation('+')}>+</Button>

    <Button color="light" on:click={polarity}>+/-</Button>  
    <Button color="light" on:click={() => addNumber('0')}>0</Button>
    <Button color="light" on:click={addComma}>.</Button>
    <Button color="blue" on:click={equal}>=</Button>
  </div>

</main>

<style lang="postcss">
  .calculator {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: repeat(5, 50px);
    gap: 10px;
  }

  .total {
    overflow: auto;
  }
</style>
