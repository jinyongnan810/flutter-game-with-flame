# Flutter game by Flame

## From

codelab:
https://codelabs.developers.google.com/codelabs/flutter-flame-game

repo: https://github.com/jinyongnan810/flutter-game-with-flame

## Main Game

1. create a class which extends `FlameGame`
2. create an instance: `final Game game = DoodleDash();`
3. use GameWidget as the entry point

```dart
GameWidget(
  game: game,
  overlayBuilderMap: <String, Widget Function(BuildContext, Game)>{
    'gameOverlay': (context, game) => GameOverlay(game),
    'mainMenuOverlay': (context, game) => MainMenuOverlay(game),
    'gameOverOverlay': (context, game) => GameOverOverlay(game),
  },
)
```

## Game Control Object

- create classes like `class GameManager extends Component with HasGameRef<DoodleDash>`
- keep status as properties
- access in the main game class like

```dart
LevelManager levelManager = LevelManager(); // manages levels and its differences
GameManager gameManager = GameManager(); // manages characters and score
ObjectManager objectManager = ObjectManager(); // manages spawning objects
```

- add like normal Components: ` await add(gameManager);`

## Create dynamic background

```dart
class World extends ParallaxComponent<DoodleDash> {
  @override
  Future<void> onLoad() async {
    parallax = await gameRef.loadParallax(
      [
        ParallaxImageData('game/background/06_Background_Solid.png'),
        ParallaxImageData('game/background/05_Background_Small_Stars.png'),
        ParallaxImageData('game/background/04_Background_Big_Stars.png'),
        ParallaxImageData('game/background/02_Background_Orbs.png'),
        ParallaxImageData('game/background/03_Background_Block_Shapes.png'),
        ParallaxImageData('game/background/01_Background_Squiggles.png'),
      ],
      fill: LayerFill.width,
      repeat: ImageRepeat.repeat,
      baseVelocity: Vector2(0, -5),
      velocityMultiplierDelta: Vector2(0, 1.2),
    );
  }
}
// use like normal Component add in onLoad
final World _world = World();
await add(_world);
```
