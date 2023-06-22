// Variables

const submitButton = document.querySelector('input[type="submit"]')
const form = document.querySelector('form')

const nomeElement = document.querySelector('input#nome')
const emailElement = document.querySelector('input#email')
const nascElement = document.querySelector('input#nasc')
const sexoElement = document.querySelector('input#m')
const estCivilElement = document.querySelector('select#estCivil')
const interesseElement = document.querySelector('#analise')

// Handler functions

function handleError({ element, msg }) {
  msg && window.alert('🟥 ' + msg)
  element && element.focus()
}

function handleSuccess({ name, form }) {
  window.alert(`✅ Obrigado, ${getFirstName(name)}! Seus dados foram enviados com sucesso.`)
  form.reset()
}

// Support functions

function getFirstName(fullName) {
  return fullName.split(/\s+/g)[0]
}

function getAge(date) {
  // converte a string da data para formato 'date'
  const dateOfBirth = new Date(date)

  // calcula a diferença de meses em relação ao tempo atual
  const monthDiff = Date.now() - dateOfBirth.getTime()

  // converte a diferença calculada para o formato 'data'
  const ageDate = new Date(monthDiff)

  // extrai o ano da data
  const userYear = ageDate.getUTCFullYear()

  // calcula a idade do usuário
  const age = userYear - 1970

  return age
}

// Validation functions

function validateName(name) {
  if (name.trim() === '') {
    handleError({ element: nomeElement, msg: 'Preencha o campo de nome.' })
    return false
  }

  if (name.replace(/\s+/g, ' ').trim().length < 15) {
    handleError({ element: nomeElement, msg: 'Nome deve conter ao menos 15 letras.' })
    return false
  }

  return true
}

function validateEmail(email) {
  if (email.trim() === '') {
    handleError({ element: emailElement, msg: 'Preencha o campo de e-mail.' })
    return false
  }

  if (!email.match(/^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/g)) {
    handleError({ element: emailElement, msg: 'Formato de e-mail inválido.' })
    return false
  }

  if (email.length <= 10) {
    handleError({ element: emailElement, msg: 'E-mail deve ter ao menos 10 caracteres.' })
    return false
  }

  return true
}

function validateBirthday(date) {
  const year = date.split('-')[0]

  if (!date) {
    handleError({ element: nascElement, msg: 'Preencha o campo de data de nascimento.' })
    return false
  }

  if (getAge(date) <= 0 || getAge(date) >= 110) {
    handleError({ element: nascElement, msg: 'Insira uma data válida.' })
    return false
  }

  return true
}

function validateSex(sex) {
  if (!sex) {
    handleError({ element: sexoElement, msg: 'Selecione um sexo.' })
    return false
  }

  return true
}

function validateMaritialStatus(status, date) {
  if (!status) {
    handleError({ element: estCivilElement, msg: 'Selecione um estado civil.' })
    return false
  }

  if (status === 'solteiro' && getAge(date) <= 15) {
    handleError({
      element: estCivilElement,
      msg: 'Estado civil de solteiro pede idade mínima de 15 anos.',
    })
    return false
  }

  return true
}

function validateInterests(interests = []) {
  if (interests.filter((interest) => interest).length === 0) {
    handleError({ element: interesseElement, msg: 'Selecione ao menos um interesse.' })
    return false
  }

  return true
}

// Main function

function handleSubmit(e) {
  e.preventDefault()

  const formData = new FormData(form)
  const data = Object.fromEntries(formData)


  const interests = [
    data.analise,
    data.bancoDados,
    data.desenvolvimento,
    data.engSoftware,
    data.redesComp,
  ]

  if (!validateName(data.nome)) return
  if (!validateEmail(data.email)) return
  if (!validateBirthday(data.nasc)) return
  if (!validateSex(data.sexo)) return
  if (!validateMaritialStatus(data.estCivil, data.nasc)) return
  if (!validateInterests(interests)) return

  handleSuccess({ name: data.nome, form: form })
}

// Event Listeners

form.onsubmit = handleSubmit
