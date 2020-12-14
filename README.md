# code-review-cypress
# code-review-cypress
Além da revisão de código padrão:
* Verificar se comandos de debbuging foram retirados do código.
* Verificar se os comandos .only e .skip foram retirados.
* Verificar se não há esperas de um tempo fixo(anti-pattern), usar em último caso, preferir usar a biblioteca cypress-wait-until. Ex.: cy.wait(10000) e Thread.Sleep(10000)
https://docs.cypress.io/api/commands/wait.html#Time
* Verificar se há condicionais e se são realmente necessárias. https://docs.cypress.io/guides/core-concepts/conditional-testing.html#Definition 
* Se possível, baixar a branch e estressar o teste por linha de comando e em modo headless para saber se não há testes intermitentes, principalmente se existir uma condicional. 
```bash
npx cypress run --browser chrome --headless
``` 
* Verificar se os elementos foram mapeados com data-cy. Verificar se há abstração de ações comuns no hooks (before, beforeEach, After e AfterEach). 
* Verificar se o .find está sendo usado corretamente
 Ex.: 

  ❌
  ```javascript
  // encontrando um elemento
  cy.get('#nav').find('header').find('.user').as('user') 
  ```
  ✔

  ```javascript
  // encontrando um elemento 
  cy.get('#nav header .user').as('user')  
  ``` 
  ✔

  ```javascript
  // encontrando um elemento filho de um elemento  
  // que foi mapeado que interage com outras funções 
  cy.get('#todos li').first().as('firstTodo') 
  cy.get('@firstTodo').find('.edit').click() 
  cy.get('@firstTodo').should('have.class', 'editing')
    .find('input')
    .type('Clean the kitchen')   
  ```

* Verifique se, ao invés de usar const, let ou var, foi atribuído um alias para chamar um elemento ou dados de verificação. https://docs.cypress.io/guides/core-concepts/variables-and-aliases.html#Sharing-Context Ex.: 
  * 1. Compartilhando um elemento 
  ```javascript
  beforeEach(() => {  
  // alias the $btn.text() as 'text'
  cy.get('button').invoke('text').as('tex  t') }) 

  it('has access to text', function () { 
    this.text // is now available
  })  
  ``` 
  * 2. Compartilhando o texto de um botão
  ```javascript
  beforeEach(() => {   
  // alias the $btn.text() as 'text'
    cy.get('button').invoke('text').as('text') 
  }) 

  it('has access to text', function () {
    this.text // is now available })
  })  
  ```  
  * 3. Compartilhando dados de uma fixture

  ```javascript
  beforeEach(() => {   
  // alias the users fixtures  
    cy.fixture('users.json').as('users') 
  })

  it('utilize users in some way', function () {   
    // access the users property
    const user = this.users[0]

    // make sure the header contains the first
    // user's name
    cy.get('header').should('contain', user.name) 
  }) 
  ```
