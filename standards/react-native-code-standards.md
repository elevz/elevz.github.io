# React Native — Padrões de Código

> Documento de referência genérico, reutilizável em qualquer projeto React Native. Não descreve um app específico — define como o código deve ser escrito, nomeado e testado.

---

## 1. Idioma

- Todo o código (nomes de variáveis, funções, componentes, arquivos, tipos, chaves de objeto/JSON, mensagens de commit) deve ser escrito em **inglês**.
- Apenas o conteúdo visível ao usuário final (textos de tela, labels, mensagens de erro exibidas na UI) deve estar em **português**, e deve vir sempre de um arquivo de constantes de strings — nunca hardcoded dentro de JSX.

---

## 2. Comentários

- Não adicionar comentários (`//`, `/* */`) explicando o que o código faz. O código deve ser autoexplicativo através de nomes claros de variáveis, funções e componentes.
- Exceção única: comentários do tipo `TODO` são aceitáveis para marcar itens de escopo futuro diretamente no ponto do código onde serão implementados.

---

## 3. Convenções de nomenclatura

Distinção importante: o **nome do arquivo** segue kebab-case com sufixo indicando sua finalidade; o **identificador exportado dentro do arquivo** (nome do componente, hook, tipo) segue a convenção de código normal (PascalCase ou camelCase).

| Elemento | Convenção de arquivo | Exemplo de arquivo | Identificador interno |
|---|---|---|---|
| Pasta de feature | `kebab-case` | `user-profile/` | — |
| Tela | `kebab-case` + `.screen.tsx` | `user-profile.screen.tsx` | `UserProfileScreen` |
| Hook | `kebab-case`, prefixo `use-` (sem sufixo adicional — o prefixo já identifica o arquivo) | `use-user-profile.ts` | `useUserProfile` |
| Componente | `kebab-case` + `.component.tsx` | `profile-card.component.tsx` | `ProfileCard` |
| Serviço | `kebab-case` + `.service.ts` | `analytics.service.ts` | `AnalyticsService` |
| Repositório | `kebab-case` + `.repository.ts` | `user-mmkv.repository.ts` | `UserRepositoryMMKV` |
| Store | `kebab-case` + `.store.ts` | `user.store.ts` | `useUserStore` |
| Tipos | `kebab-case` + `.types.ts` | `user.types.ts` | `User`, `UserRole` |
| Constantes | `kebab-case` + `.constants.ts` | `storage-keys.constants.ts` | `SCREAMING_SNAKE_CASE` |
| Tema | `kebab-case` + `.theme.ts` | `colors.theme.ts` | `camelCase` |
| Utilitário | `kebab-case` + `.util.ts` | `date-formatter.util.ts` | `camelCase` |
| Teste | mesmo nome do arquivo testado + `.test.ts(x)` (ver seção 6 para localização) | `profile-card.component.test.tsx` | — |
| Mock manual | mesmo nome do módulo mockado | `react-native-mmkv.ts` (dentro de `__mocks__`) | — |
| Chave de armazenamento (MMKV/AsyncStorage) | — | — | `camelCase` com prefixo de domínio, ex: `user.profile` |

---

## 4. Tipos de dados (TypeScript)

- Preferir `interface` para formas de objeto que representam entidades de domínio; `type` para uniões, interseções e aliases simples.
- Nomear tipos de forma descritiva, sem prefixo (`I`, `T`) — `User`, não `IUser`.
- Evitar `any`. Quando o tipo genuinamente não é conhecido, usar `unknown` e validar antes de usar.

```typescript
type UserRole = 'admin' | 'member' | 'guest';

interface User {
  id: string;
  name: string;
  role: UserRole;
}
```

---

## 5. Tratamento de erros

- Toda operação de I/O (rede, armazenamento local, sistema de arquivos) deve estar em bloco `try/catch`, nunca deixar exceção não tratada propagar até a UI sem uma mensagem amigável.
- Erros voltados ao usuário devem usar mensagens centralizadas no arquivo de strings, em português, nunca expor mensagem de exceção bruta na tela.
- Falhas que deixariam o estado da aplicação inconsistente (ex: referência a um recurso que falhou ao salvar) devem impedir a conclusão da operação, não deixar dado parcial persistido.

---

## 6. Testes

### 6.1 Estrutura e localização

- Todos os arquivos de teste ficam centralizados em uma pasta `__tests__` na raiz do projeto (`/__tests__`), **não** colocados ao lado do arquivo testado.
- A estrutura interna de `__tests__` espelha a estrutura de `/src`, para manter fácil localizar o teste de qualquer arquivo.
- Todos os mocks manuais ficam centralizados em `/__mocks__` na raiz do projeto, seguindo a convenção de mocks do Jest (mock de módulo `node_modules/<pacote>` fica em `__mocks__/<pacote>.ts`; mock de módulo interno espelha o caminho relativo a partir de `__mocks__`).

```
/__tests__
  /features
    /user-profile
      user-profile.screen.test.tsx
      use-user-profile.test.ts
      /components
        profile-card.component.test.tsx
  /services
    /storage
      user-mmkv.repository.test.ts
  /store
    user.store.test.ts
  /utils
    validators.util.test.ts
/__mocks__
  react-native-mmkv.ts
  react-native-fs.ts
  /services
    /storage
      user-mmkv.repository.ts
/src
  /features
    /user-profile
      user-profile.screen.tsx
      use-user-profile.ts
      /components
        profile-card.component.tsx
  /services
    /storage
      user.repository.ts
      user-mmkv.repository.ts
  /store
    user.store.ts
  /utils
    validators.util.ts
```

### 6.2 O que testar

- Toda função de lógica de negócio (repositórios, validadores, utilitários, hooks com lógica não trivial) deve ter teste unitário correspondente.
- Cobertura mínima esperada:
  - **Repositórios**: criar, listar, buscar por id, atualizar, deletar, incluindo casos de dado inexistente ou operação que falha.
  - **Validadores/utilitários**: casos de entrada válida, entrada inválida e valores-limite (string vazia, lista vazia, valores nulos).
  - **Componentes de tela**: pelo menos um teste de renderização (`renders correctly`) e um teste por interação principal (ex: `calls onSave when form is valid`).
- Testes de UI não devem testar detalhes de implementação (estilo, estrutura interna do DOM) — apenas comportamento visível/observável pelo usuário (texto renderizado, callback chamado, navegação disparada).

### 6.3 Convenção de nomes de teste

- Nomes de teste em inglês, descrevendo o comportamento esperado, nunca a implementação.
- Formato preferido: `it('does X when Y')` ou `it('returns Z given W')`.

```typescript
it('returns null when user id does not exist')
it('calls onSave when form is valid')
it('throws when storage write fails')
```

### 6.4 Ferramentas

- `Jest` como test runner.
- `React Testing Library` (RTL) para testes de componente, priorizando queries por texto/role acessível em vez de `testID` sempre que possível.

---

## 7. Centralização de variáveis (sem valores soltos no código)

- **Cores**: nunca hex direto espalhado pelos componentes — centralizar em `theme/colors.theme.ts`.
- **Espaçamentos, tamanhos de fonte, raio de borda**: centralizar em `theme/tokens.theme.ts`.
- **Textos exibidos ao usuário**: centralizar em `constants/strings.constants.ts`, evitando texto solto direto no JSX.
- **Chaves de armazenamento e nomes de diretórios de mídia**: centralizar em `constants/storage-keys.constants.ts`, nunca strings soltas espalhadas nas chamadas ao repositório.
- **Limites numéricos** (tamanhos máximos, contagens, timeouts): centralizar em `constants/limits.constants.ts`.

---

## 8. Estrutura de pastas

Organização por **feature**, isolando tela (layout), lógica (hook) e componentes locais de cada fluxo. Elementos reaproveitáveis entre features ficam nas pastas globais (`components`, `hooks`, `services`, `theme`, `constants`, `types`, `utils`).

```
/src
  /features
    /<feature-name>
      <feature-name>.screen.tsx
      use-<feature-name>.ts
      /components
        <local-component>.tsx
  /components
    /layout
      card.tsx
      screen.tsx
    /form
      button.tsx
      text.tsx
    /overlay
      modal.tsx
      alert.tsx
  /hooks
    use-theme.ts
  /services
    /storage
      <entity>.repository.ts
      <entity>-mmkv.repository.ts
    /navigation
      root.navigator.tsx
      navigation.types.ts
  /store
    <entity>.store.ts
    /<entity>
      <entity>.types.ts
      use-<entity>.ts
      <entity>.store.ts
  /theme
    colors.ts
    tokens.ts
  /constants
    strings.ts
    storage-keys.ts
    limits.ts
  /types
    <entity>.ts
  /utils
    validators.ts
App.tsx
/__tests__
  (espelha /src, ver seção 6.1)
/__mocks__
  (ver seção 6.1)
```

**Critério para decidir onde colocar algo**:
- Só é usado por uma única feature → vive dentro de `/src/features/<feature>/components`.
- Usado por duas ou mais features (ex: um `Button` genérico) → sobe para `/src/components` global.
- Lógica de acesso a dados externos ao React (armazenamento, sistema de arquivos, rede, navegação) → vive em `/src/services`, nunca dentro de uma feature.
- Estado compartilhado entre features → vive em `/src/store`, consumido pelos hooks de cada feature.

---

## 9. Gerenciamento de estado

- Preferir um store por domínio/entidade (Zustand), expondo apenas ações que internamente chamam o repositório correspondente — as telas nunca acessam o repositório diretamente, apenas através do store.

```typescript
interface EntityStore {
  items: Entity[];
  loadItems: () => void;
  createItem: (item: Entity) => void;
  updateItem: (item: Entity) => void;
  deleteItem: (id: string) => void;
}
```

---

## 10. Arquitetura de acesso a dados

- Toda persistência (local ou remota) deve ficar atrás de uma interface de repositório, nunca ser chamada diretamente pela UI ou pelo store.
- Isso permite trocar a implementação (ex: de armazenamento local para um backend) sem reescrever a camada de UI.

```typescript
interface EntityRepository {
  list(): Entity[]
  save(entity: Entity): void
  findById(id: string): Entity | null
  remove(id: string): void
}

class EntityRepositoryMMKV implements EntityRepository { /* ... */ }
```

---

## 11. Commits

- Seguir o padrão **Conventional Commits**: `<type>(<scope opcional>): <description>`.
- `type` sempre em inglês, minúsculo, dentre os valores abaixo:

| Tipo | Uso |
|---|---|
| `feat` | Nova funcionalidade |
| `fix` | Correção de bug |
| `refactor` | Mudança de código sem alterar comportamento externo |
| `test` | Adição ou ajuste de testes |
| `chore` | Manutenção que não afeta código de produção (configuração, dependências) |
| `docs` | Mudança em documentação |
| `style` | Formatação, espaçamento — sem mudança de lógica |
| `perf` | Melhoria de performance |
| `build` | Mudança relacionada a build ou dependências externas |
| `ci` | Mudança em configuração de integração contínua |

- `description` em inglês, no imperativo presente (`add`, não `added` ou `adds`), começando em minúsculo, sem ponto final.
- `scope` opcional identifica a feature ou camada afetada, alinhado aos nomes de pasta definidos na seção 8 (ex: `user-profile`, `storage`, `theme`).
- Breaking changes: adicionar `!` após o tipo/escopo (`feat(storage)!: change repository interface`) e detalhar o impacto no corpo do commit.
- Corpo do commit (opcional, linha em branco após a primeira linha) explica o "porquê" da mudança quando não for óbvio — não repete o "o quê", que já está no título.

```
feat(user-profile): add avatar upload support
fix(storage): handle missing file on repository read
refactor(theme): extract spacing tokens from color file
test(validators): cover empty string edge case
chore: update react-native to 0.76
```