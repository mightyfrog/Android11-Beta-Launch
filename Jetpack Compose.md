# Jetpack Compose

<https://www.youtube.com/watch?v=U5BwfqBpiWU>

```kotlin
@Composable
fun ProductLabel(product: Product) {
    Text("${product.quantity}x ${product.name}") // called "emitting"
}
```

Provide data to function to generate UI.

f(data) -> UI

```kotlin
@Composable
fun ProductLabel(product: Product) {
    if (product.quantity > 0) {
        Text("${product.quantity}x ${product.name}")
    }
}
```

## State 3:00

```kotlin
fun ProductFilter(products: List<Product>) {
    var filter by state {""}

    FilledTextField(
        value = filter,
        onValueChange = { filter = it },
        label = { Text("Name:") }
    )

    for (product in products) {
        if (product.name.contains(filter)) {
            ProductLabel(product)
        }
    }
}
```

## Tech Stack 4:30

- Development Host
  - Kotlin Compiler
  - Compose Compiler Plugin
  - Android Studio
- Device
  - Compose Runtime
  - Compose UI Core
  - Compose UI Foundation
  - Compose UI Material

## Roadmap... so far 5:40

- May 2019 - Composed announced and moved to AOSP
- October 2019 - Developer Preview 1
- June 2020 - Developer Preview 2
- Summer 2020 - Alpha
- 2021 - 1.0

bi-weekly release

## Modifiers 8:00

Modifiers decorate a single element. They can supply layout params, and additional behaviors.

```kotlin
val (shape, setShape) = state<Shape> { CircleShape }
Image(asset = imageResource(R.drawable.sample_landscape),
    modifier = Modifier
        .size(256.dp)
        .padding(16.dp)
        .drawShadow(8.dp, shape)
        .drawBorder(6.dp, MaterialTheme.colors.primary, shape)
        .drawBorder(12.dp, MaterialTheme.colors.secondary, shape)
        .drawBorder(18.dp, MaterialTheme.colors.background, shape)
        .ripple(color = MaterialTheme.colors.onSurface)
        .clickable {
            setShape(
                if (shape == CircleShape) {
                    CutCornerShape(topLeft = 32.dp, bottomRight = 32.dp)
                } else {
                    CircleShape
                }
            )
        }
```

## Lists 10:38

```kotlin
@Composable
fun ShoppingCart (
    shoppingCart: LiveData<List<Product>>
) {
    val products by shoppingCart.observerAsState(emptyList())
    AdapterList(
        data = products
    ) { product ->
        ShoppingCartItem(product) {
            Viewer3rd(product)
        }
    }
}
```

## ConstraintLayout 12:20

```kotlin
@Composable
fun ShoppingCartItemRow(/*...*/) {
    ConstraintLayout(
        constraintSet = ConstraintSet {
            val decreaseConstraint = tag(DecreaseTag).apply {
                left constrainTo parent.left
                centerVertically()
            }
        }
            val increaseConstraint = tag(IncreaseTag).apply {
                left constrainTo decreaseConstraint.right
                left.margin 4.dp
                centerVertically()
            }
        }
    ) {
        SmallButton(modifier = Modifier.tag(DecreaseTag)) {
            Image(Icons.Sharp.Remove)
        }
        SmallButton(modifier = Modifier.tag(IncreaseTag)) {
            Image(Icons.Sharp.Remove)
        }
    }
}
```

## Animations 15:05

```kotlin
val (selected, onSelected) = state { false }

val tlRadius = animate(if (selected) 48.dp else 8.dp)
val radius = animate(if (selected) 0.dp else 8.dp)

Surface(
    shape = RoundedCornerShape(
        topLeft = rlRadius,
        topRight = radius, bottomLeft = radius, bottomRight = radius
    )
) {
    Toggleable(
        value = selected,
        onValueChange = onSelected,
        modifier = Modifier.ripple()
    ) {
        // ...
    }
}
```

```kotlin
val selectedAlpha = animate(if (selected) 0.65f else 0.0f)

Surface(
    modifier = Modifier.matchParentSize(),
    color = MaterialTheme.colors.primary.copy(alpha = selectedAlpha)
) {
    Icon(
        asset = Icons.Filled.Done,
        tint = contentColor().copy(alpha = selectedAlpha)
    )
}
```

## Interoperability 17:30

```kotlin
@Composable
fun Viewer3d(product: Product) {
    var modelViewer by state<ModelViewer?> { null }

    onActive {
        // Setup Choreographer callback
        onDispose {
            // Remove Choreographer callback
        }
    }

    onCommit(product) {
        // Set product color on 3D scene
    }

    AndroidView(R.layout.surface_3d) { view ->
        surfaceView = view as SurfaceView
        modelViewer = setupModelViewer(surfaceView)
    }
}
```

## Testing 20:00

```kotlin
@Composable
fun ShoppingCartItem(
    product: Product,
    decrease: (Product) -> Unit = { }
) {
    // ...

    Row {
        TestTag(tag = Tags.ShoppingCartItemDecrease) {
            SmallButton(onClick = { decrease(product) }) {
                Image(Icons.Sharp.Remove)
            }
        }
        // ...
    }
}
```

```kotlin
@Test
fun changeQuantity() {
    var product = Product(quantity = 2)
    val decrease: (Product) -> Unit = { product = it.copy(quantity = it.quantity - 1) }

    composeTestRule.setContent {
        ShoppingCartItem(
            product = product,
            decrease = decease
        )
    }

    findByTag(Tags.ShoppingCartItemDecrease)
        .doClick()
        .doClick()

    runOnIdleCompose {
        assertThat(product.qunatity).isEqualTo(0)
    }
}
```

## Resource


[Jetpack Compose DP2](https://developer.android.com/jetpack/compose)

[Samples](https://github.com/android/compose-samples)

[Slack](slack.kotlinlang.org) (#compose)
