# Custom Floor Selector

To implement a custom floor selector, we expect you to already have completed the [Getting Started Tutorial](https://docs.mapsindoors.com/sdks-and-frameworks/web/getting-started/tutorial).

This guide will walk you through the process of implementing a custom floor selector using the MapsIndoors JavaScript SDK. The custom floor selector provides a more flexible and visually appealing way to switch between floors in your MapsIndoors-enabled application.

### Prerequisites

Before you begin, make sure you have completed the [getting started tutorial](https://docs.mapsindoors.com/sdks-and-frameworks/web/getting-started/tutorial) for the MapsIndoors JavaScript SDK. You should have a basic MapsIndoors map set up in your project.

### Implementation

#### Step 1: Create the CustomFloorSelector Class

First, we'll create a `CustomFloorSelector` class that will handle the creation and management of our custom floor selector.

```javascript
class CustomFloorSelector {
    constructor(mapsIndoorsInstance) {
        this.mapsIndoors = mapsIndoorsInstance;
        this.element = this.createSelectorElement();
        this.floors = {};
    }

    createSelectorElement() {
        const container = document.createElement('div');
        container.style.cssText = `
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(30, 30, 30, 0.8);
            padding: 10px;
            border-radius: 15px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
            display: flex;
            flex-direction: column;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s ease;
        `;
        return container;
    }

    // ... (other methods will be added here)
}
```

#### Step 2: Implement Floor Management Methods

Add methods to handle showing, hiding, and updating the floor selector:

```javascript
class CustomFloorSelector {
    // ... (previous code)

    onShow() {
        this.element.style.display = 'flex';
        this.updateFloorButtons();
    }

    onHide() {
        this.element.style.display = 'none';
    }

    updateFloors(floors) {
        if (floors) {
            this.floors = Object.entries(floors).reduce((acc, [index, floorInfo]) => {
                acc[index] = floorInfo.name || `Floor ${index}`;
                return acc;
            }, {});
        } else {
            this.floors = {};
        }
        this.updateFloorButtons();
    }

    updateWithCurrentBuilding() {
        const currentBuilding = this.mapsIndoors.getBuilding();
        if (currentBuilding) {
            this.updateFloors(currentBuilding.floors);
        } else {
            this.updateFloors(null);
        }
    }
}
```

#### Step 3: Implement Floor Button Creation and Updating

Add methods to create and update the floor buttons:

```javascript
class CustomFloorSelector {
    // ... (previous code)

    updateFloorButtons() {
        this.element.innerHTML = ''; // Clear existing buttons
        const currentFloor = this.mapsIndoors.getFloor();

        if (Object.keys(this.floors).length === 0) {
            const noFloorsMessage = document.createElement('div');
            noFloorsMessage.textContent = 'No floors available';
            noFloorsMessage.style.cssText = `
                color: #DDD;
                font-family: 'Arial', sans-serif;
                font-size: 14px;
                padding: 10px;
            `;
            this.element.appendChild(noFloorsMessage);
            return;
        }

        // Sort floor indices in descending order
        const sortedFloors = Object.entries(this.floors)
            .sort(([a], [b]) => Number(b) - Number(a));

        sortedFloors.forEach(([floorIndex, floorName]) => {
            const button = document.createElement('button');
            button.textContent = floorName;
            button.style.cssText = `
                margin: 5px 0;
                padding: 10px 20px;
                border: none;
                background-color: ${floorIndex === currentFloor ? 'rgba(200, 160, 40, 0.8)' : 'rgba(60, 60, 60, 0.6)'};
                color: ${floorIndex === currentFloor ? '#FFF' : '#CCC'};
                cursor: pointer;
                font-family: 'Arial', sans-serif;
                font-size: 16px;
                font-weight: bold;
                border-radius: 10px;
                transition: all 0.2s ease;
                text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
                outline: none;
            `;
            button.onmouseover = () => {
                if (floorIndex !== currentFloor) {
                    button.style.backgroundColor = 'rgba(80, 80, 80, 0.8)';
                    button.style.color = '#FFF';
                }
            };
            button.onmouseout = () => {
                if (floorIndex !== currentFloor) {
                    button.style.backgroundColor = 'rgba(60, 60, 60, 0.6)';
                    button.style.color = '#CCC';
                }
            };
            button.onclick = () => this.changeFloor(floorIndex);
            this.element.appendChild(button);
        });
    }

    changeFloor(floorIndex) {
        this.mapsIndoors.setFloor(floorIndex);
        this.updateFloorButtons();
    }
}
```

#### Step 4: Initialize MapsIndoors and CustomFloorSelector

Now, let's initialize MapsIndoors and our custom floor selector:

```javascript
//Previous code from Getting Started Guide

// Initialize CustomFloorSelector
const customFloorSelector = new CustomFloorSelector(mapsIndoorsInstance);
document.body.appendChild(customFloorSelector.element);
```

#### Step 5: Set Up Event Listeners

Finally, set up event listeners to update the floor selector when necessary:

```javascript
// Set up event listeners for MapsIndoors
mapsIndoorsInstance.addListener('ready', () => {
    customFloorSelector.onShow();
    customFloorSelector.updateWithCurrentBuilding();
});

mapsIndoorsInstance.addListener('floor_changed', () => {
    customFloorSelector.updateFloorButtons();
});

mapsIndoorsInstance.addListener('building_changed', () => {
    customFloorSelector.updateWithCurrentBuilding();
});

// Log any MapsIndoors errors
mapsIndoorsInstance.addListener('error', (error) => {
    console.error("MapsIndoors error:", error);
});
```

### Customization

You can customize the appearance of the floor selector by modifying the CSS styles in the `createSelectorElement` and `updateFloorButtons` methods. Adjust colors, fonts, sizes, and positioning to match your application's design.
