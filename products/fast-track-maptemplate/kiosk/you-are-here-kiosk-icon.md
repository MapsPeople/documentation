# 'You Are Here' Kiosk Icon

### **How to Configure a “You Are Here” Kiosk Icon**

When you have multiple kiosks on the same map, you want each kiosk to display **only its own** “You Are Here” icon. The setup below ensures that the kiosk shows its location without cluttering the map with icons from other kiosks.

#### **Steps**

1. **Create a Location Type dedicated for the functionality**
   * Create a Location Type and give it a meaningful name e.g., _Kiosk-you-are-here_).
   * Set it to:
     * Not selectable
     * Not searchable
     * Normal behavior otherwise
2. **Disable General Visibility, turn Icon Visibility On and assign xoom**
   *   Turn **General Visibility = Off**.

       This prevents the POI from appearing in the map by default and keeps it out of search results.
   * Enable visibility of the icon for the Location type.
   * Set the zoom level at which the icon should appear.
3. **Assign a Custom Icon**
   * Apply a custom “You Are Here” icon (use an approved internal asset).
   * Note that the size of the icon will impact the size on the map. It will enlarge it, when using it as a kiosk origin ID, so play around with it till you find a size that you prefer.
   * It is recommended to have the “You Are Here” icon slightly larger than the other icons on the map.
4. **Create One POI per Kiosk**
   * Add a POI for each physical kiosk and place them on the map.
   * Each POI should use the Location Type created above.
5. **Use kioskOriginLocationId in the Web App**
   * When loading the kiosk application:
     * Provide the kioskOriginLocationId for the kiosk being used.
     * The kiosk logic forces **that specific** POI to be visible, even though general visibility is disabled.
   * Other kiosk POIs stay hidden because the app does not activate them.

#### **Result**

Each kiosk shows only its own “You Are Here” marker. No extra icons appear, even if multiple kiosk POIs exist on the map.

#### Demo

{% embed url="https://drive.google.com/file/d/1kU41aWtZhpGfy7oYnbN26Sc62utk7G2B/preview" %}
