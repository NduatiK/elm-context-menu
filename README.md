# elm-context-menu

A elm-ui port of jinjor's elm-contextmenu


How to use

This component works with The Elm Architecture.

<span>1. Add the context menu state to your model</span>
```elm
-- We will only setup a single function to handle all the page's context menus.
-- To differentiate where the user clicked, we will also provide some Context
-- This can be anything, but Custom Types work perfectly
type Context
    = RootView
    | ChildView

type alias Model =
  { contextMenu : ContextMenu Context
  , message : String
  , ...
  }
```

<span>2. Add two messages, one handles the view state, the other is a message to be emitted when an option is selected</span>
```elm
type Msg
  = ContextMenuMsg (ContextMenu.Msg Context)
  | ItemSelected Int
```


<span>3. Initialize the model state and send out an initialization message</span>
```elm
init : Flags -> (Model, Cmd Msg)
init flags =
  let
    (contextMenu, msg) = ContextMenu.init
  in
    ( { contextMenu = contextMenu
      }
    , Cmd.map ContextMenuMsg msg
    )
```


<span>4. Handle the changes to the context menu and item selections</span>
```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    ContextMenuMsg msg ->
      let
        (contextMenu, cmd) =
          ContextMenu.update msg model.contextMenu
      in
        ( { model | contextMenu = contextMenu }
        , Cmd.map ContextMenuMsg cmd
        )
    ItemSelected number ->
         -- Do something better
        (model, msg)

```


<span>5. Subscribe to context menu events</span>
```elm
subscriptions : Model -> Sub Msg
subscriptions model =
  Sub.map ContextMenuMsg (ContextMenu.subscriptions model.contextMenu)
```


<span>6. View</span>
```elm
view : Model -> Html Msg
view model =
  div
    -- Attach right click event
    -- When we right click, the toItemGroups function will receive RootView
    [ ContextMenu.open ContextMenuMsg RootView ]
    -- Render a context menu when it is open
    [ ContextMenu.view
        ContextMenu.defaultConfig
        ContextMenuMsg
        toItemGroups
        toItemGroups model.contextMenu
    ]

toItemGroups : Context -> List (List Item)
toItemGroups context =
  if context == RootView then
    [ [ (ContextMenu.item "Hey", ItemSelected 1)
      , (ContextMenu.item "Yo!", ItemSelected 2)
      ]
    ]
  else
    []
```
