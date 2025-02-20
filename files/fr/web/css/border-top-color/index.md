---
title: border-top-color
slug: Web/CSS/border-top-color
tags:
  - CSS
  - Propriété
  - Reference
translation_of: Web/CSS/border-top-color
---

{{CSSRef}}

La propriété **`border-top-color`** définit la couleur utilisée pour la bordure haute d'un élément.

Pour avoir une formulation plus concise, on pourra utiliser les propriétés raccourcies {{cssxref("border-color")}} et/ou {{cssxref("border-top")}}.

{{EmbedInteractiveExample("pages/css/border-top-color.html")}}

## Syntaxe

```css
/* Valeurs de type <color> */
border-top-color: red;
border-top-color: #ffbb00;
border-top-color: rgb(255, 0, 0);
border-top-color: hsla(100%, 50%, 25%, 0.75);
border-top-color: currentColor;
border-top-color: transparent;

/* Valeurs globales */
border-top-color: inherit;
border-top-color: initial;
border-top-color: unset;
```

### Valeurs

- `<color>`
  - : Une valeur de couleur (type {{cssxref("&lt;color&gt;")}}) qui décrit la couleur utilisée pour la bordure du côté haut.

### Syntaxe formelle

{{csssyntax}}

## Exemples

### CSS

```css
.maboite {
  border: solid 0.3em gold;
  border-top-color: red;
  width: auto;
}

.texterouge {
  color: red;
}
```

### HTML

```html
<div class="maboite">
  <p>Une boîte avec une bordure autour.
     Notez le côté de la boîte qui est
     <span class="texterouge">rouge</span>.</p>
</div>
```

### Résultat

{{EmbedLiveSample('Exemples')}}

## Spécifications

| Spécification                                                                                            | État                                     | Commentaires                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| {{SpecName('CSS3 Backgrounds', '#border-top-color', 'border-top-color')}}         | {{Spec2('CSS3 Backgrounds')}} | Pas de modification significative, la valeur `transparent` a été supprimée car elle fait désormais partie du type {{cssxref("&lt;color&gt;")}} qui a été étendu. |
| {{SpecName('CSS2.1', 'box.html#propdef-border-top-color', 'border-top-color')}} | {{Spec2('CSS2.1')}}                 | Définition initiale.                                                                                                                                                     |

{{cssinfo}}

## Compatibilité des navigateurs

{{Compat("css.properties.border-top-color")}}

## Voir aussi

- Les propriétés raccourciées liées aux bordures

  - {{cssxref("border")}},
  - {{cssxref("border-top")}},
  - {{cssxref("border-color")}}.

- Les propriétés de couleur pour les autres bordures :

  - {{cssxref("border-right-color")}},
  - {{cssxref("border-bottom-color")}},
  - {{cssxref("border-left-color")}}.

- Les autres propriétés liées à la bordure du haut :

  - {{cssxref("border-top-style")}},
  - {{cssxref("border-top-width")}}.
