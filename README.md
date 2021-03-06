# Dobre Praktyki w React

Dobre Praktyki w React

> Staram na bierząco uzupełniać wiadomości.\
> Jeżeli znasz inne dobre praktyki,\
> które znacząco podnoszą poziom kodu i ułatwiają prace, \
> [skonatkuj się z mną], lub [stwórz pull request].\
> Z chęcią poszerzę swoje horyzonty z React!

### 1. Stosować destrykturyzację wszędzie gdzie to tylko możliwe

```bash
1. const [item1, item2, ...items] = ArrayOfItems
2. const {item1, item2:{id, content}} = props
3. const FunctionComponents = ({item, log: { _id, priority, text, user, created }}) => {...........});
```

### 2. Single Source of Truth 
Unikać lokalnego stanu.\
Mamy kilka rozwiązan:
- Redux 
- MobX 
- Context API -> createContext, useReducer, useContext, react-query
- Recoil - od twórców Facebook-a, więc pewnie będzie mocno rozwijana.

> Lokalny stan (useState) stosować do komponentów UI.

### 3. DRY - Don't repeat yourself.

> by uniknąć powtarzania kody nalęzy mapować komponenty

```bash
.....
<ListWrapper>
  {arrayOfItems.map( item => <ListItem key={item.id} {...item} /> )}
</ListWrapper>
```

### 4. Pamiętać, by przy mapowaniu tablicy nadawać key={} elementom.

> Key ma być uniklany tylko względem mapowanej tablicy a nie całej aplikacji.\
> React wykorzystuje zjawisko pojednania (ang. reconciliation). \
> Reconcilation jest możliwe dzięki algorytmowi który został użyty w React. [React’s “diffing” algorithm]

```bash
arrayOfItems.map(item => <li key={item.id}>{item.content}</li>)
```

### 5. Togglowanie stanu w kompnentach UI zamiast pisania dwóch funkcji.

> setState działa asynchorniczne, więc by zabezpieczyć się przed ewentulanymi błedami przy togglowaniu powinno się przekazać poprzedni stan.

```js
this.setState((state) => ({ value: state.value + 1}));
```

### 6. Poprawny zgodny z konwencjami zapis:

```bash
1. HOC - withCompoenent
2. Custom Hook - useMyHook
3. nazwa komponentu z dużej litery
```

### 7. "Łapiąc" elementy w testach używamy `data-testid="sample`, a nie "łapiemy" po klasie :

```bash
test("render input element", () => {
    render(<Input />);
    expect(screen.getByTestId("sample-input")).toBeInTheDocument();
   });
```

### 8. Stworzyć plik jsconfig.json w głownym folderze projektu, a w nim dodać opcję bezwględnych ścieżek przy kompilacji.

```bash
{
 "compilerOptions": {
   "baseUrl": "src"
 },
 "include": [
   "src"
 ]
}
```

> Można rówież w pliku .env stworzyć poniższą regułę, ale VSC wtedy nie podpowiada.

```bash
NODE_PATH = src
```

> Niestety w Gatsbym dodatkowo trzeba stworzyć "aliasy do ścieżek" w pliku `gatsby-node.js` by kompilator je widział

```bash
exports.onCreateWebpackConfig = ({ actions }) => {
 actions.setWebpackConfig({
   resolve: {
     alias: {
       components: path.resolve(__dirname, 'src/components'),
       assets: path.resolve(__dirname, 'src/assets'),
       data: path.resolve(__dirname, 'src/data'),
     },
   },
 })
}
```

### 9. Funkcje wspierające(helpers lub utils) wyeksportować do `utils/index.js`

### 10. Single Responsibility Principle

> Zasada Pojedyńćzego zadania.\
> Komponent powinnien wykonywać jak najbardziej zminimalizowaną liczbę zadań. \
> Przejrzystość i czytelność kodu.

### 11. Obsługa błedów

> Stworzyć w utilits `setError(true, error)` którego odpiwiednio obsłużymy,

```bash
try{
// fetch
}catch(error){
setError(true, error)
}
```

> React wychodzi z rozwiązaniem - [Error Boundarie]

```bash
<ErrorBoundary>
 <MyCommponent />
</ErrorBoundary>
```

### 12. Memonizacja - useMemo(), useCallback() lub użyć HOC jakim jest React.memo().

> Memonizacja jest to zabieg, który ma na celu poprawić wydajność naszego kodu poprzez zapamiętywanie budowy / wartości komponentu.\
> Jeżeli propsy bądź waartosći przekazane się nie zmienią kompnent nie będzie ponowanie renderowany.

### 13. Walidować propsy i nadaniawe domyślnych wartości

> Poprzez PropTypes \
> Poprzez TypeScript :heartpulse:

### 14. Nie pracować na wartościach typu string.

> Łatwo popełnić literówki. \
> Jeżli potrzebujemy wartości do porównywania tworzymy plik constans.js, który przetrzymuje nam zmienne.\
> Konwecja mówi, by pisać tego typu zmienne wielkimi literami.\
> Jest to sygnał dla osoby czytającej nasz kod, że jest to zmienna stworzona do porównywania.

```bash
 -> `constans.js`
export const SAMPLE = 'SAMPLE'
```

### 15. useEffect i useLayoutEffect

> useEffect działa asynchronicznie, więc używamy go w 99% przypadkach. \
> useLayoutEffect działa synchronicznie wraz rendrem komponentu. Używamy jeżeli potrzebujemy manipulować elementami DOM i dokonywać pomiarów. \
> [@n8tb1t/use-scroll-position] - super hook do dokonywania pomiarów, wykorzystuje useLayoutEffect. \
> Pamiętać by odmontowywać eventy `clearInterval`, `removeEventListener` by utrzymać performance na wysokim poziomie.

### 16. Obsługa SSR 
> Gdy piszemy skrypt (np. Hook), który może być reużywalny w wielu projektach to należy go zabezpiczyć by obsługiwał SSR(Next.js)
```bash
const isBrowser = typeof window !== `undefined`
const useCustomHook = () => {
  if (!isBrowser) return something
......
......
}

```

### 17. Obsługa CORS w developmencie
> Odpalając serwer oraz aplikację frontendową lokalnie (localhost) robimy to na różnych portach np. :5000 i :3000. \
> Podczas zapytań do servera dochodzi więc do probelmu z CORS. \
> By obsłużyć politykę CORS podczas developmentu, należy dodać do package.json aplikacji frontendowej proxy z informacją na jakim porcie znajduje się nasz serwer. \ 
> **"proxy": "http://localhost:<port_serwera>"** - np: `"proxy": "http://localhost:5000"`. \
> Następnie np. poprzez axios wystarczy tworzyć zapaytania podawając tylko odpowiednią ścieżkę. \
> `const res = await axios.post('api/auth/login', body, config);`

### 18. Obsługa CSP w Create React App
> `By default, Create React App will embed the runtime script into index.html during the production build.`
> Narusza więc to politykę Content-Security-Policy. \
> By tego uniknąć w naszym kliencie do pliku .env dodajemy `INLINE_RUNTIME_CHUNK=false` co rozwiązuje proeblem c CSP.
> Dodatkowo jeżli na sewerze buduje się nasz aplikacja to możemy zrobić - `.... && INLINE_RUNTIME_CHUNK=false npm run build`


[skonatkuj się z mną]: https://www.linkedin.com/in/igor-dudek-96a87611a/
[stwórz pull request]: https://github.com/dudek-igor/Dobre_Praktyki_w_React/pulls
[error boundarie]: https://medium.com/swlh/understanding-reacts-error-boundaries-c15db8229d97
[@n8tb1t/use-scroll-position]: https://www.npmjs.com/package/@n8tb1t/use-scroll-position
[React’s “diffing” algorithm]:https://pl.reactjs.org/docs/reconciliation.html
