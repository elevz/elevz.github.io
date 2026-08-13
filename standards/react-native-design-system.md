# React Native — Design System

> Documento de referência genérico, reutilizável em qualquer projeto React Native. Define tokens visuais e padrões de interface — não descreve um app específico. Complementa `react-native-code-standards.md` (ver seção 8 daquele documento para onde esses tokens vivem no código: `theme/colors.theme.ts`, `theme/tokens.theme.ts`).

---

## 1. Princípios

- **Acessibilidade em primeiro lugar**: todo par texto/fundo deve respeitar contraste mínimo AA do WCAG. Áreas de toque nunca abaixo de 44x44dp.
- **Poucos elementos por tela**: priorizar respiro visual e hierarquia clara sobre densidade de informação.
- **Consistência sobre originalidade**: reutilizar os mesmos tokens (cor, espaçamento, raio) em todo o app, evitando valores soltos criados ad-hoc por tela.
- **Ícone nunca sozinho**: elementos interativos com ícone devem ter rótulo de texto ou `accessibilityLabel`, nunca depender só da interpretação visual do ícone.

---

## 2. Paleta de cores

Paleta base neutra e quente, pensada para alto contraste. Pode ser adaptada por projeto (ver seção 2.2), mas a estrutura de papéis (roles) deve se manter.

### 2.1 Paleta padrão (tema claro)

| Token | Valor | Papel |
|---|---|---|
| `background` | `#F5F5F5` | Fundo geral das telas |
| `surface` | `#FFFFFF` | Cards, campos de input |
| `textPrimary` | `#1A1A1A` | Títulos, corpo de texto principal |
| `textSecondary` | `#6B6858` | Legendas, metadados |
| `border` | `#D8D3C4` | Bordas de card e input |
| `primary` | `#0B5FA5` | Ação primária — botões principais, links |
| `success` | `#3B6D11` | Confirmações, estados positivos |
| `danger` | `#A32D2D` | Ações destrutivas, erros |
| `warning` | `#8C6D1F` | Alertas não críticos |

Todos os pares `textPrimary`/`textSecondary` sobre `background`/`surface`, e texto branco sobre `primary`/`success`/`danger`, devem ser verificados contra WCAG AA (contraste mínimo 4.5:1 para texto normal, 3:1 para texto grande/ícones).

### 2.2 Adaptando a paleta por projeto

Ao aplicar esse sistema a um novo projeto, a **cor `primary`** é o principal ponto de customização (identidade visual do app). As demais cores (fundo, superfície, texto, bordas) devem se manter próximas do padrão acima, a menos que o projeto exija um tema visualmente distinto — trocar `primary` já é suficiente para diferenciar a identidade sem quebrar consistência de legibilidade.

### 2.3 Cor por tipo de conteúdo (quando aplicável)

Quando o app distingue tipos de mídia ou categoria de conteúdo, usar cor consistente por tipo em vez de cores arbitrárias por tela:
- Conteúdo visual (foto/vídeo) → `primary`
- Conteúdo sonoro (áudio) → `success`
- Ações destrutivas → `danger`

---

## 3. Tipografia

| Token | Tamanho | Uso |
|---|---|---|
| `fontSizeCaption` | 12px | Legendas, metadados secundários |
| `fontSizeBody` | 15–16px | Texto padrão de UI |
| `fontSizeTitle` | 17–18px | Títulos de tela, títulos de card |
| `fontSizeReading` | 20px | Conteúdo principal em telas de leitura/consumo |

- Peso de fonte: `500` (medium) para títulos e botões; `400` (regular) para corpo de texto.
- Sempre respeitar a escala de fonte configurada no sistema (`allowFontScaling: true`), sem limitar `maxFontSizeMultiplier` a menos que quebre o layout de forma severa.
- Altura de linha: `1.4`–`1.5` para blocos de texto corrido (melhora legibilidade, especialmente em `fontSizeReading`).

---

## 4. Espaçamento e layout

| Token | Valor | Uso |
|---|---|---|
| `spaceXs` | 4px | Espaço entre elementos muito próximos (ícone + label) |
| `spaceSm` | 8px | Espaço interno pequeno |
| `spaceMd` | 12–16px | Espaço padrão entre elementos e padding de card |
| `spaceLg` | 20–24px | Espaço entre seções de tela |
| `spaceXl` | 32px+ | Separação de blocos grandes, topo/fundo de tela |

- Padding padrão de tela: `16px` nas laterais.
- Gap padrão entre itens de lista: `8`–`10px`.

---

## 5. Raio de borda

| Token | Valor | Uso |
|---|---|---|
| `radiusSm` | 8px | Elementos pequenos (badges, ícones em container) |
| `radiusMd` | 12px | Cards, botões, inputs — padrão da maioria dos elementos |
| `radiusLg` | 20px | Containers grandes, modais, folhas (sheets) |

---

## 6. Acessibilidade — checklist de interface

- [ ] Contraste de todo par texto/fundo verificado contra WCAG AA.
- [ ] Área de toque mínima de 44x44dp em todo elemento interativo.
- [ ] `accessibilityLabel` e `accessibilityRole` definidos em botões, inputs e elementos customizados (não nativos).
- [ ] Fonte respeitando escala do sistema (`allowFontScaling`).
- [ ] Nenhuma informação transmitida **só** por cor (ex: erro não deve depender apenas de ficar vermelho — acompanhar de ícone e/ou texto).
- [ ] Gestos complexos (arrastar, pinça, multi-toque) sempre com alternativa de toque simples, quando a ação for essencial ao fluxo.
- [ ] Ordem de foco lógica para navegação por leitor de tela (TalkBack/VoiceOver).

---

## 7. Padrões de componentes

### 7.1 Botão primário
- Fundo `primary`, texto branco, `radiusMd`, altura mínima 48px.
- Estado desabilitado: reduzir opacidade (`~50%`), nunca remover o elemento do layout.

### 7.2 Card
- Fundo `surface`, borda `border` (0.5–1px), `radiusMd`, padding `spaceMd`.
- Elevação/sombra sutil opcional — preferir borda a sombra pesada, mantendo visual limpo e consistente entre plataformas.

### 7.3 Input de texto
- Fundo `surface`, borda `border`, `radiusMd`, padding interno `spaceMd`.
- Estado de erro: borda `danger` + mensagem de erro em texto abaixo do campo (nunca só a cor da borda mudando).

### 7.4 Estado vazio (empty state)
- Mensagem central curta, tom convidativo (não apenas "nenhum item encontrado") — orientar a próxima ação do usuário.

### 7.5 Navegação entre passos/telas sequenciais
- Botões de avançar/voltar com área de toque ampliada (mínimo 48px de altura), posicionados de forma consistente (ex: sempre na parte inferior da tela).
- Indicador de progresso (barra ou contador "X de Y") quando o fluxo tiver múltiplas etapas.

---

## 8. Onde isso vive no código

Seguindo a estrutura definida em `react-native-code-standards.md`:

```
/src
  /theme
    colors.theme.ts
    tokens.theme.ts
```

```typescript
export const colors = {
  background: '#FAF8F3',
  surface: '#FFFFFF',
  textPrimary: '#1A1A1A',
  textSecondary: '#6B6858',
  border: '#D8D3C4',
  primary: '#0B5FA5',
  success: '#3B6D11',
  danger: '#A32D2D',
  warning: '#8C6D1F',
};
```

```typescript
export const tokens = {
  spacing: { xs: 4, sm: 8, md: 16, lg: 24, xl: 32 },
  radius: { sm: 8, md: 12, lg: 20 },
  fontSize: { caption: 12, body: 16, title: 18, reading: 20 },
};
```

Componentes nunca devem referenciar valores hex ou números soltos diretamente — sempre importar de `colors` e `tokens`.