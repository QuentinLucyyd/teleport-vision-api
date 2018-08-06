# teleportHQ Vision API
![alt text](https://i.imgur.com/aOGNl7K.jpg "teleportHQ Vision API")
**teleportHQ Vision API** is a computer vision API specifically trained for **detecting atomic UI elements in pictures** of hand-drawn wireframes (as seen in the picture above).

The list of elements it can distinguish: `paragraph, label, header, button, checkbox, radiobutto, rating, toggle, dropdown, listbox, textarea, textinput, datepicker, stepperinput, slider, progressbar, image, video`.
## Using the Vision API
### Request
Send all requests to the API endpoint (base url): `https://europe-west1-croapp-dev.cloudfunctions.net/visionApiMiddleware`
#### Request header
Make sure to add a `Content-Type` key with the value `application/json` and a `Teleport-Token` key with the key provided by us.
#### Request body
The body of the request is a json with two keys: `image` and `threshold`.
* `image` is a **required** string parameter that denotes the direct url to a jpg or png image.
* `threshold` is an *optional* parameter. **Default** value is `0.1`. The detection model outputs a confidence score for each detection (between 0 and 1) and won't include in the response detections with confidence lower than this threshold.

Request body example:
```
{
    "image": "https://i.imgur.com/eF9KN8U.jpg", 
    "threshold": 0.5
}
```
#### Request example
```
curl -d '{"image": "https://i.imgur.com/eF9KN8U.jpg", "threshold": 0.5}' \
     -H "Teleport-Token: 123" -H "Content-Type: application/json" \ 
     -X POST https://europe-west1-croapp-dev.cloudfunctions.net/visionApiMiddleware
```

### Response
If your request is a valid one, you will recieve back a json with the following structure:

```
[
    {
        "box": [x, y, width, height],
        "detectionClass": numeric_label,
        "detectionString": string_label,
        "score": confidence_rating
    },
    ...
]
```
The json contains a list of objects, each one of this objects corresponding to a detected atomic UI element in the image sent in the request.
* `box` contains the coordinates of the bounding box surrounding the detected element. `x` and `y` are the coordinates of the top left corner of the box and `width` and `height` are self explanatory. All coordinates are **normalized between [0, 1]** where `(0,0)` is the top left corner of your image and `(1, 1)` is the bottom right corner. In other words, if you want to get the pixel coordinates you have to multiply `x` and `width` with the width of your image and `y` and `height` with the height of your image.
* `detectionClass` is the numeric class of the detection.
* `detectionString` is the human-readable label of the detection.
* `score` represents how confident the algorithm is that the predicted object is a correct / valid one. It takes values between `[0, 1]`, where `1` represents a 100% confidence in its detection.

The `detectionClass` to `detectionString` mapping is done according to this dictionary:
```
{
    1: "paragraph",
    2: "label",
    3: "header",
    4: "button",
    5: "checkbox",
    6: "radiobutton",
    7: "rating",
    8: "toggle",
    9: "dropdown",
    10: "listbox",
    11: "textarea",
    12: "textinput",
    13: "datepicker",
    14: "stepperinput",
    15: "slider",
    16: "progressbar",
    17: "image",
    18: "video"
}
```

#### Response example
```
[
    {
        "box": [
            0.161091,
            0.524945,
            0.714086,
            0.783918
        ],
        "detectionClass": 17,
        "detectionString": "image",
        "score": 0.999926
    },
    {
        "box": [
            0.332949,
            0.203727,
            0.571151,
            0.459328
        ],
        "detectionClass": 11,
        "detectionString": "textarea",
        "score": 0.999782
    },
    {
        "box": [
            0.885467,
            0.332858,
            0.952213,
            0.443467
        ],
        "detectionClass": 4,
        "detectionString": "button",
        "score": 0.998915
    }
]
```
## How do I get a Teleport-Token?
If you are interested in using this API, feel free to get in touch with us via [email](mailto:paul.brie@teleporthq.io), [twitter](https://twitter.com/TeleportHQio) or [LinkedIn](https://www.linkedin.com/company/teleporthq/). 