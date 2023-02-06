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
// add and remove overlay like
overlays.remove('mainMenuOverlay');
overlays.add('gameOverOverlay');
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

## Create characters and objects

```dart
// 1. create object states
enum PlayerState {
  left,
  right,
  center,
  rocket,
  nooglerCenter,
  nooglerLeft,
  nooglerRight
}
// 2. create object class with states
class Player extends SpriteGroupComponent<PlayerState>
    with HasGameRef<DoodleDash>
// 3. add like all other objects
// 4. change appearance according to state
Future<void> _loadCharacterSprites() async {
  // Load & configure sprite assets
  final left = await gameRef.loadSprite('game/${character.name}_left.png');
  final right = await gameRef.loadSprite('game/${character.name}_right.png');
  final center =
      await gameRef.loadSprite('game/${character.name}_center.png');
  final rocket = await gameRef.loadSprite('game/rocket_4.png');
  final nooglerCenter =
      await gameRef.loadSprite('game/${character.name}_hat_center.png');
  final nooglerLeft =
      await gameRef.loadSprite('game/${character.name}_hat_left.png');
  final nooglerRight =
      await gameRef.loadSprite('game/${character.name}_hat_right.png');

  sprites = <PlayerState, Sprite>{
    PlayerState.left: left,
    PlayerState.right: right,
    PlayerState.center: center,
    PlayerState.rocket: rocket,
    PlayerState.nooglerCenter: nooglerCenter,
    PlayerState.nooglerLeft: nooglerLeft,
    PlayerState.nooglerRight: nooglerRight,
  };
}
```

## Moving the objects

```dart
// update per frame
@override
void update(double dt) {
  // check is movable
  if (gameRef.gameManager.isIntro || gameRef.gameManager.isGameOver) return;

  // set velocity in x axis (_hAxisInput came from user input)
  _velocity.x = _hAxisInput * jumpSpeed;
  final double dashHorizontalCenter = size.x / 2;

  // take care of walls
  if (position.x < dashHorizontalCenter) {
    position.x = gameRef.size.x - (dashHorizontalCenter);
  }
  if (position.x > gameRef.size.x - (dashHorizontalCenter)) {
    position.x = dashHorizontalCenter;
  }

  // add gravity by add y+ velocity
  _velocity.y += _gravity;

  // change position by velocity
  position += _velocity * dt;
  super.update(dt);
}

// set immediate velocity by user input
void jump({double? specialJumpSpeed}) {
  _velocity.y = specialJumpSpeed != null ? -specialJumpSpeed : -jumpSpeed;
}

// force position
void resetPosition() {
  position = Vector2(
    (gameRef.size.x - size.x) / 2,
    (gameRef.size.y - size.y) / 2,
  );
}
```

## Handling collisions

## User inputs

## Camera and spawning new objects
