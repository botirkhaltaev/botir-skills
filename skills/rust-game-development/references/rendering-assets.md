# Rendering & Assets

## 2D Sprites

```rust
fn setup(mut commands: Commands, asset_server: Res<AssetServer>) {
    // Camera
    commands.spawn(Camera2d::default());

    // Sprite from file
    commands.spawn((
        Sprite::default(),
        Transform::from_xyz(0.0, 0.0, 0.0),
        asset_server.load::<Image>("sprites/player.png"),
    ));

    // Colored rectangle
    commands.spawn((
        Sprite {
            color: Color::srgb(0.9, 0.2, 0.2),
            custom_size: Some(Vec2::new(50.0, 50.0)),
            ..default()
        },
        Transform::from_xyz(100.0, 0.0, 0.0),
    ));
}
```

## Sprite Sheets (Texture Atlas)

```rust
fn setup(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlasLayout>>,
) {
    let texture = asset_server.load("spritesheet.png");
    let layout = TextureAtlasLayout::from_grid(UVec2::new(32, 32), 8, 4, None, None);
    let layout_handle = texture_atlases.add(layout);

    commands.spawn((
        Sprite::default(),
        texture,
        TextureAtlas {
            layout: layout_handle,
            index: 0,
        },
        AnimationTimer(Timer::from_seconds(0.1, TimerMode::Repeating)),
    ));
}

fn animate_sprites(
    time: Res<Time>,
    mut query: Query<(&mut AnimationTimer, &mut TextureAtlas)>,
) {
    for (mut timer, mut atlas) in &mut query {
        timer.0.tick(time.delta());
        if timer.0.just_finished() {
            atlas.index = (atlas.index + 1) % 32;  // cycle frames
        }
    }
}
```

## Asset Loading

```rust
#[derive(Resource)]
struct GameAssets {
    player_sprite: Handle<Image>,
    font: Handle<Font>,
    sound_effect: Handle<AudioSource>,
}

fn load_assets(mut commands: Commands, asset_server: Res<AssetServer>) {
    commands.insert_resource(GameAssets {
        player_sprite: asset_server.load("player.png"),
        font: asset_server.load("fonts/FiraSans-Bold.ttf"),
        sound_effect: asset_server.load("sounds/explosion.ogg"),
    });
}
```

## UI (Bevy UI)

```rust
fn setup_ui(mut commands: Commands) {
    commands.spawn((
        Node {
            width: Val::Percent(100.0),
            height: Val::Px(50.0),
            justify_content: JustifyContent::Center,
            align_items: AlignItems::Center,
            ..default()
        },
        BackgroundColor(Color::srgba(0.0, 0.0, 0.0, 0.5)),
    )).with_children(|parent| {
        parent.spawn((
            Text::new("Score: 0"),
            TextFont {
                font_size: 24.0,
                ..default()
            },
            ScoreText,
        ));
    });
}

fn update_score_text(
    score: Res<Score>,
    mut query: Query<&mut Text, With<ScoreText>>,
) {
    if score.is_changed() {
        for mut text in &mut query {
            **text = format!("Score: {}", score.0);
        }
    }
}
```

## Audio

```rust
fn play_sound(mut commands: Commands, asset_server: Res<AssetServer>) {
    commands.spawn(AudioPlayer::new(
        asset_server.load("sounds/hit.ogg"),
    ));
}

// Background music
fn start_music(mut commands: Commands, asset_server: Res<AssetServer>) {
    commands.spawn((
        AudioPlayer::new(asset_server.load("music/theme.ogg")),
        PlaybackSettings::LOOP,
    ));
}
```

## 3D Basics

```rust
fn setup_3d(
    mut commands: Commands,
    mut meshes: ResMut<Assets<Mesh>>,
    mut materials: ResMut<Assets<StandardMaterial>>,
) {
    // Camera
    commands.spawn((
        Camera3d::default(),
        Transform::from_xyz(0.0, 5.0, 10.0).looking_at(Vec3::ZERO, Vec3::Y),
    ));

    // Light
    commands.spawn((
        DirectionalLight {
            illuminance: 10000.0,
            shadows_enabled: true,
            ..default()
        },
        Transform::from_rotation(Quat::from_euler(EulerRot::XYZ, -0.5, 0.5, 0.0)),
    ));

    // Mesh
    commands.spawn((
        Mesh3d(meshes.add(Cuboid::new(1.0, 1.0, 1.0))),
        MeshMaterial3d(materials.add(StandardMaterial {
            base_color: Color::srgb(0.8, 0.2, 0.2),
            ..default()
        })),
        Transform::from_xyz(0.0, 0.5, 0.0),
    ));
}
```
