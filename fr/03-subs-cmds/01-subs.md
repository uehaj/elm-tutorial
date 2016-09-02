# Souscriptions

En Elm, les __souscriptions__ permettent à votre application d'écouter ce qui se passe à l'extérieur. Par exemple :

- [Les évènements clavier](http://package.elm-lang.org/packages/elm-lang/keyboard/latest/Keyboard)
- [Les mouvements de souris](http://package.elm-lang.org/packages/elm-lang/mouse/latest/Mouse)
- Les changements de page/url du navigateur
- [Les évènements Websocket](http://package.elm-lang.org/packages/elm-lang/websocket/latest/WebSocket)

Pour essayer ça, nous allons créer une application qui répond aux évènements du clavier et de la souris.

D'abord, installez les librairies :

```bash
elm package install elm-lang/mouse
elm package install elm-lang/keyboard
```

Et créez ce programme :

```elm
module Main exposing (..)

import Html exposing (Html, div, text)
import Html.App
import Mouse
import Keyboard


-- MODEL


type alias Model =
    Int


init : ( Model, Cmd Msg )
init =
    ( 0, Cmd.none )



-- MESSAGES


type Msg
    = MouseMsg Mouse.Position
    | KeyMsg Keyboard.KeyCode



-- VIEW


view : Model -> Html Msg
view model =
    div []
        [ text (toString model) ]



-- UPDATE


update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        MouseMsg position ->
            ( model + 1, Cmd.none )

        KeyMsg code ->
            ( model + 2, Cmd.none )



-- SUBSCRIPTIONS


subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.batch
        [ Mouse.clicks MouseMsg
        , Keyboard.presses KeyMsg
        ]



-- MAIN


main : Program Never
main =
    Html.App.program
        { init = init
        , view = view
        , update = update
        , subscriptions = subscriptions
        }
```

Exécutez ce programme avec Elm reactor : à chaque fois que vous cliquez sur la souris le compteur augmente d'un, à chaque fois que vous appuyez sur une touche le compteur augmente de 2.

---

Passons en revue les parties importantes de ce programme.

### Messages

```elm
type Msg
    = MouseMsg Mouse.Position
    | KeyMsg Keyboard.KeyCode
```

Il y a deux messages possibles : `MouseMsg` et `KeyMsg`. Ils vont respectivement être déclenclés lorsque la souris ou le clavier sont enclenchés.

### Update

```elm
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        MouseMsg position ->
            ( model + 1, Cmd.none )

        KeyMsg code ->
            ( model + 2, Cmd.none )
```

Notre fonction `update` répond à chaque message de manière différente, ainsi elle augmente le compteur de un lorsque l'on clique sur la souris, et de deux lorsque que l'on appuie sur une touche.

### Souscriptions

```elm
subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.batch ➌
        [ Mouse.clicks MouseMsg ➊
        , Keyboard.presses KeyMsg ➋
        ]
```

Ici nous déclarons les choses que nous souhaitons écouter. Nous voulons écouter les évènements `Mouse.clicks` ➊ et `Keyboard.presses` ➋. Ces deux fonctions prennent en paramètre un constructeur de message et retournent une souscription.

Nous utilisons `Sub.batch` ➌ de pouvoir écouter les deux évènements. `Batch` prend en paramètre une liste de souscriptions et retourne une souscription qui inclut toutes les autres.
