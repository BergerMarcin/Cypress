# Cypress
Moja ulubiona apka/lib do testów front-end'owych
<hr/>

### 0 Wprowadzenie
Cypress to front-endowa apka do testów automatycznych (**end-to-end, integration & unit tests**) 
symulująca działanie użytkownika przeglądarki 
([krótki 28s filmik z testowania za pomocą Cypress](https://docs.cypress.io/guides/overview/why-cypress.html#Running-tests) ).<br/>
Może być też wykorzystywana do scrapowania, itp. <br/> 
Cypress korzysta z doświadczeń Mocha i Chai, dzięki czemu jest dojrzałym narzędziem testów front-endowych, 
a próg wejścia jest niski nie tylko dla tych, którzy posługiwali się **Mocha** i **Chai**, ale też dla wszystkich "nowych osób" 
(jest naprawdę ***"nieprzekombinowany"***, ale można też nim nie źle poczarować i skomplikować kod).<br/>
Po napisaniu kodu testru najwygodniejsze jest użytkowanie Cypress z jego graficznego panelu (panel sam skanuje 
nasz projekt w poszukiwaniu testów Cypressowych i listuje je nam dając możliwość wyboru jak i uruchomienia całej serii). 
Dodatkowo panel Cypress przy pierwszym uruchomieniu (`npx cypress open`) instaluje ***przykłady testów*** w katalogu projektowym Cypress. <br/>
Ponadto jest też terminalowa apka uruchamiająca testy (mniej wygodna, ale mogąca wykonywać testy uruchamiane zupełnie automatycznie).<br/>
Serie testów można też zakodować w package.json (w script'cie).

>Wymagania:
> - kod Cypress pisze się w **JavaScript** <br/>
> - Cypress wymaga **Node.js>8.0**
<hr/>

### I Zasada działania Cypress
Generalnie Cypress działa jako niezależna aplikacja, która po wybraniu przez testera testu:
 - uruchamia wybraną przeglądarkę,
 - przechodzi do danej strony wg podanego w kodzie testu url,
 - symuluje działanie zwykłego użytkownika (poprzez eventy),
 - na koniec (lub na etapach pośrednich) sprawdza efekt symulacji/działania "zwykłego użytkownika" tzn. sprawdza czy 
 jest zgodny z oczekiwaniem (np. czy na stronie wyświetlił się dany tekst)<br/>
 
Testy są stosunkowo wolno uruchamiane, bo Cypress wykorzystuje do wyświetlania strony technologię canvas (tj. grafiki a nie html)
Oczywiście i tak działa nie współmiernie szybciej niż tester ręczny.<br/>

Wszystkie metody Cypress są "pod spodem" Promisami i działają jak await, co oznacza, że z definicji czekają na wykonanie 
danego eventu czy na sprawdzenie przez nastawialny maksymlany czas (po przekroczeniu którego Cypress oczywiście rzuca błąd). 
To pozwala uniknąć problemów z wolno działającymi bazami czy wolnym połączeniem z serwerem oraz znajdować i debugować 
wolne operacje (Cypress wyświetla w terminalu czas danej operacji).<br/>
Dzięki temu też Cypress wykonuje kod linia po linii czekając na wykonanie uprzedniej linii (jak to ma miejsce przy 
await z JavaScript ES>8)
<hr/> 

### II Instalacja i ... dobre przykłady
Najlepiej zainstalować Cypress ([link od instalacji Cypress](https://docs.cypress.io/guides/getting-started/installing-cypress.html#Installing)) 
w podkatalog głównego rootsa projektu (ale nie w katalogu frontu, bo to generalnie osobna apka)<br/>
Przy pierwszym uruchomieniu panelu Cypress (`npx cypress open`) od razu dodawane są dobre przykłady w katalogu Cypress 
(dla mnie najciekawsze są w `/integration/examples`)
<hr/>

### III Uruchamianie (z panelem graficznym Cypress)
1. NAJPIERW NALEŻY ***URUCHOMIĆ SAMĄ TESTOWANĄ APKĘ WEB'ową*** (całość tj. backend i front, o ile są podzielone)
2. POTEM z terminala, z katalogu Cypress należy ***uruchomić graficzny panel Cypress*** za pomocą: `npx cypress open` 
3. DALEJ ***z panelu Cypress należy uruchomić dany test w danej przeglądarce*** (należy wybrać daną przeglądarkę w 
prawym górnym rogu panelu Cypress; można to też ustawić w package.json lub zakodować w teście)

Dany test pojawia się w nowym oknie wybranej przeglądarki z podziałem na Cypressową konsolę śledzącą test i okno 
samej uruchomionej strony/apki (w technologii canvas)
Test jest wykonywany i jeżeli wszystko jest OK, to komunikaty w konsoli są zielone. 
W przeciwnym wypadku w Cypressowej konsoli przeglądarki wskazana jest linia kodu, której nie udało się wykonać 
lub asercja/sprawdzenie, które zakończyło się negatywnie.
<hr/>

### IV Kod Cypress
(poniższy przykład znajduje się całościowo w [rozdziale V przykład 1](###chapter51))

1.Deklaracja testu
```
describe('Test description', () => {        //test description & start of callback arrow function 
```

2.Przejście na daną stronę wg url (stronę na ogół z apką webową), np.:
```
  cy.visit('http://localhost:8080/');
```

3.Symulacja jakiegoś działania użytkownika. Te działania/symulacje należy wykonać na danym elemencie html pobranym 
ze strony z wykorzystaniem selektorów jak w JS/jQuery/AJAX (na marginesie: Cypress ma wbudowany jQuery) 
Poniżej przykład z logowaniem:
```
  cy.get('[data-cy=username]').type('Marcin Berger');   //fill-in/typing into element with attribute data-cy of value username
  cy.get('[data-cy=password]').type('Berger123');       //fill-in/typing into element with attribute data-cy of value password
  cy.get('[data-cy=login_button]').click();             //click on element with attribute data-cy of value login_button 
```
Oczywiście opcji symulowania działania użytkownika jest mnóstwo (doczytaj w dokumentacji Cypress). 
Powyżej podano tylko wprowadzanie tekstu i klikanie.

4.Gdy już coś tam się zadziało (Cypress sam czeka, bo wszystko realizuje przez Promisy), to możemy sprawdzić czy 
faktycznie się zadziało (np.: przez sprawdzenie/asercję czy na stronie pojawił się jakiś tekst)
Przykład asercji czy pojawiło się powitanie z nazwą użytkownika:
```
  cy.contains('Witaj Marcin Berger!').should('be.visible');   //check if text appeared (is visible) in browser
```

5.I to wszystko :) (należy już tylko zamknąć funkcję callback i samą funkcję)
<br/><br/><br/>
A zaraz, jeszcze html:
> **Kod Html - nieobowiązkowe rady** <br/>
> Z uwagi na selektory najlepiej w kodzie html/JS Twojej testowanej apki dodać do danego elementu atrybut data-cy="some-name" np.:
> ```
> <form>
>   <input type="username" data-cy="username"/>
>   <input type="password" data-cy="password"/>
>   <button type="submit" data-cy="login_button">Dalej</button>
> </form>
> ```
<hr/>

### V Przykłady "całościowe"
#### <a name="chapter51"></a>Przykład 1 do powyższego rozdziału IV
***login_test.js*** *(plik z kodem testu do umieszczenia w katalogu Cypress)*
```
describe('User can login after open page', () => {
  cy.visit('http://localhost:8080/');

  cy.get('[data-cy=username]').type('Marcin Berger');   //fill-in/typing into element with attribute data-cy of value username
  cy.get('[data-cy=password]').type('Berger123');       //fill-in/typing into element with attribute data-cy of value password
  cy.get('[data-cy=login_button]').click();             //click on element with attribute data-cy of value login_button

  cy.contains('Witaj Marcin Berger!').should('be.visible');   //check if text appeared (is visible) in browser
});
```

***przykładowy kod html*** *(który będzie serwowany pod danym url)*
```
<form>
  <input type="username" data-cy="username"/>
  <input type="password" data-cy="password"/>
  <button type="submit" data-cy="login_button">Dalej</button>
</form>
```

#### Przykład 2 - sprawdzenie czy działa Vuex'owy store (przechowujący i mutujący globalne stany) we Vue.js
```
describe('At development mode I should have Vuex store data', () => {
  const getStore = () => cy.window().its('app.$store')
  const getState = () => getStore().its('state')

  it('I am checking existing Vuex store objects with properties and methods', () => {
    getStore().should("be.a", "object");
    getStore().its('_actions').should('exist');
    getStore().its('_mutations').should('exist');
    getStore().its('getters').should('exist');

    getState().should("be.a", "object");
    getState().should("have.any.keys", 'auth_code');
    getState().should("have.any.keys", 'results');
  });
});
```
