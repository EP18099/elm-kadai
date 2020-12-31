# readme
## このプログラムについて
このプログラムはElmと呼ばれる言語を使用しています。このプログラムは図形のドラッグ移動を実現するもので、実行すると以下の結果が得られます。
* 実行直後、画面左上に青色の四角形が表示される。
* 図形内にマウスカーソルが入っている状態で図形をドラッグ移動させようとすると、図形の中心にカーソルがある状態で図形がカーソルを追従する。
* ドラッグを中止すると図形はその場にとどまる。
* 図形外でクリックしても図形は反応しない。
* 図形をクリックしている間(ドラッグしている間)、図形は緑色になる。クリックしていない時は赤色になる。(初期色である青は、一度クリックして他の色に変化すると二度と戻らないようになっています)
* 高速でマウスカーソルを移動させても図形は付いてくる。
以上の機能を実現したプログラムとなっています。
## ソースコード
プログラムのソースコードをここに記載しておきます。
プログラムのソースコード↓
```
import Browser
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Json.Decode exposing (map2, field, float)

main = Browser.sandbox
  { init=init
  , view=view
  , update=update
  }

type Msg = NoOp
         | Drag
         | NotDrag
         | Move Float Float

type alias Model =
  { color:String
  , x:Float
  , y:Float
  , drag:Bool
  }
init : Model
init = 
  { color = "blue"
  , x = 0
  , y = 0
  , drag = False
  }

update : Msg -> Model -> Model
update msg model =
  case msg of
    Drag -> {model | color = "green", drag = True}
    NotDrag -> {model | color = "red", drag = False}
    Move x y ->
      { model | 
        x = if model.drag then 
          x-50
        else
          model.x
      , y = if model.drag then 
          y-50
        else
          model.y
      }
    _ -> model

view : Model -> Html Msg
view model =
  div[style "background" "white"
    , style "height" "100vh"
    , on "mousemove" (map2 Move (field "clientX" float) (field "clientY" float))
    ]
    [ div[style "position" "relative" 
      ,  style "background" model.color
      ,  style "height" "100px"
      ,  style "width" "100px"
      ,  style "top" <| String.fromFloat model.y ++ "px"
      ,  style "left" <| String.fromFloat model.x ++ "px"
      ,  onMouseDown Drag
      ,  onMouseUp NotDrag
      ]
      []
    ]
```
