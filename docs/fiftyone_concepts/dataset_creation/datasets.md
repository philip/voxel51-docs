# Loading Datasets From Disk [¶](\#loading-datasets-from-disk "Permalink to this headline")

FiftyOne provides native support for importing datasets from disk in a
variety of [common formats](#supported-formats), and it can be
easily extended to import datasets in
[custom formats](#custom-dataset-importer).

Note

Did you know? You can import media and/or labels from within the FiftyOne
App by installing the
[@voxel51/io](https://github.com/voxel51/fiftyone-plugins/tree/main/plugins/io)
plugin!

Note

If your data is in a custom format,
[writing a simple loop](index.md#loading-custom-datasets) is the easiest way
to load your data into FiftyOne.

## Basic recipe [¶](\#basic-recipe "Permalink to this headline")

The interface for creating a FiftyOne [`Dataset`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset "fiftyone.core.dataset.Dataset") for your data on disk is
conveniently exposed via the Python library and the CLI. The basic recipe is
that you simply specify the path(s) to the data on disk and the type of dataset
that you’re loading.

## Supported formats [¶](\#supported-formats "Permalink to this headline")

Each supported dataset type is represented by a subclass of
[`fiftyone.types.Dataset`](../../api/fiftyone.types.html#fiftyone.types.Dataset "fiftyone.types.Dataset"), which is used by the Python library and CLI to
refer to the corresponding dataset format when reading the dataset from disk.

| Dataset Type | Description |
| --- | --- |
| [ImageDirectory](#imagedirectory-import) | A directory of images. |
| [VideoDirectory](#videodirectory-import) | A directory of videos. |
| [MediaDirectory](#mediadirectory-import) | A directory of media files. |
| [FiftyOneImageClassificationDataset](#fiftyoneimageclassificationdataset-import) | A labeled dataset consisting of images and their associated classification labels<br>in a simple JSON format. |
| [ImageClassificationDirectoryTree](#imageclassificationdirectorytree-import) | A directory tree whose subfolders define an image classification dataset. |
| [VideoClassificationDirectoryTree](#videoclassificationdirectorytree-import) | A directory tree whose subfolders define a video classification dataset. |
| [TFImageClassificationDataset](#tfimageclassificationdataset-import) | A labeled dataset consisting of images and their associated classification labels<br>stored as TFRecords. |
| [FiftyOneImageDetectionDataset](#fiftyoneimagedetectiondataset-import) | A labeled dataset consisting of images and their associated object detections<br>stored in a simple JSON format. |
| [FiftyOneTemporalDetectionDataset](#fiftyonetemporaldetectiondataset-import) | A labeled dataset consisting of videos and their associated temporal detections in<br>a simple JSON format. |
| [COCODetectionDataset](#cocodetectiondataset-import) | A labeled dataset consisting of images and their associated object detections<br>saved in [COCO Object Detection Format](https://cocodataset.org/#format-data). |
| [VOCDetectionDataset](#vocdetectiondataset-import) | A labeled dataset consisting of images and their associated object detections<br>saved in [VOC format](http://host.robots.ox.ac.uk/pascal/VOC). |
| [KITTIDetectionDataset](#kittidetectiondataset-import) | A labeled dataset consisting of images and their associated object detections<br>saved in [KITTI format](http://www.cvlibs.net/datasets/kitti/eval_object.php). |
| [YOLOv4Dataset](#yolov4dataset-import) | A labeled dataset consisting of images and their associated object detections<br>saved in [YOLOv4 format](https://github.com/AlexeyAB/darknet). |
| [YOLOv5Dataset](#yolov5dataset-import) | A labeled dataset consisting of images and their associated object detections<br>saved in [YOLOv5 format](https://github.com/ultralytics/yolov5). |
| [TFObjectDetectionDataset](#tfobjectdetectiondataset-import) | A labeled dataset consisting of images and their associated object detections<br>stored as TFRecords in [TF Object Detection API format](https://github.com/tensorflow/models/blob/master/research/object_detection). |
| [ImageSegmentationDirectory](#imagesegmentationdirectory-import) | A labeled dataset consisting of images and their associated semantic segmentations<br>stored as images on disk. |
| [CVATImageDataset](#cvatimagedataset-import) | A labeled dataset consisting of images and their associated multitask labels<br>stored in [CVAT image format](https://github.com/opencv/cvat). |
| [CVATVideoDataset](#cvatvideodataset-import) | A labeled dataset consisting of videos and their associated multitask labels<br>stored in [CVAT video format](https://github.com/opencv/cvat). |
| [OpenLABELImageDataset](#openlabelimagedataset-import) | A labeled dataset consisting of images and their associated multitask labels<br>stored in [OpenLABEL format](https://www.asam.net/standards/detail/openlabel/). |
| [OpenLABELVideoDataset](#openlabelvideodataset-import) | A labeled dataset consisting of videos and their associated multitask labels<br>stored in [OpenLABEL format](https://www.asam.net/standards/detail/openlabel/). |
| [FiftyOneImageLabelsDataset](#fiftyoneimagelabelsdataset-import) | A labeled dataset consisting of images and their associated multitask predictions<br>stored in [ETA ImageLabels format](https://github.com/voxel51/eta/blob/develop/docs/image_labels_guide.md). |
| [BDDDataset](#bdddataset-import) | A labeled dataset consisting of images and their associated multitask predictions<br>saved in [Berkeley DeepDrive (BDD) format](https://bdd-data.berkeley.edu). |
| [CSVDataset](#csvdataset-import) | A labeled dataset consisting of images or videos and their associated field values<br>stored as columns of a CSV file. |
| [DICOMDataset](#dicomdataset-import) | An image dataset whose image data and optional properties are stored in<br>[DICOM format](https://en.wikipedia.org/wiki/DICOM). |
| [GeoJSONDataset](#geojsondataset-import) | An image or video dataset whose location data and labels are stored in<br>[GeoJSON format](https://en.wikipedia.org/wiki/GeoJSON). |
| [GeoTIFFDataset](#geotiffdataset-import) | An image dataset whose image and geolocation data are stored in<br>[GeoTIFF format](https://en.wikipedia.org/wiki/GeoTIFF). |
| [FiftyOneVideoLabelsDataset](#fiftyonevideolabelsdataset-import) | A labeled dataset consisting of videos and their associated multitask predictions<br>stored in [ETA VideoLabels format](https://github.com/voxel51/eta/blob/develop/docs/video_labels_guide.md). |
| [FiftyOneDataset](#fiftyonedataset-import) | A dataset consisting of an entire serialized [`Dataset`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset "fiftyone.core.dataset.Dataset") and its associated source<br>media. |
| [Custom formats](#custom-dataset-importer) | Import datasets in custom formats by defining your own [`Dataset`](../../api/fiftyone.types.html#fiftyone.types.Dataset "fiftyone.types.Dataset") or<br>[`DatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter "fiftyone.utils.data.importers.DatasetImporter") class. |

## ImageDirectory [¶](\#imagedirectory "Permalink to this headline")

The [`fiftyone.types.ImageDirectory`](../../api/fiftyone.types.html#fiftyone.types.ImageDirectory "fiftyone.types.ImageDirectory") type represents a directory of
images.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    <filename1>.<ext>
    <filename2>.<ext>

```

where files with non-image MIME types are omitted.

By default, the dataset may contain nested subfolders of images, which are
recursively listed.

Note

See [`ImageDirectoryImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.ImageDirectoryImporter "fiftyone.utils.data.importers.ImageDirectoryImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a directory of images as follows:

## VideoDirectory [¶](\#videodirectory "Permalink to this headline")

The [`fiftyone.types.VideoDirectory`](../../api/fiftyone.types.html#fiftyone.types.VideoDirectory "fiftyone.types.VideoDirectory") type represents a directory of
videos.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    <filename1>.<ext>
    <filename2>.<ext>

```

where files with non-video MIME types are omitted.

By default, the dataset may contain nested subfolders of videos, which are
recursively listed.

Note

See [`VideoDirectoryImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.VideoDirectoryImporter "fiftyone.utils.data.importers.VideoDirectoryImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a directory of videos as follows:

## MediaDirectory [¶](\#mediadirectory "Permalink to this headline")

The [`fiftyone.types.MediaDirectory`](../../api/fiftyone.types.html#fiftyone.types.MediaDirectory "fiftyone.types.MediaDirectory") type represents a directory of media
files.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    <filename1>.<ext>
    <filename2>.<ext>

```

Note

All files must have the same media type (image, video, point cloud, etc.)

By default, the dataset may contain nested subfolders of media files, which are
recursively listed.

Note

See [`MediaDirectoryImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.MediaDirectoryImporter "fiftyone.utils.data.importers.MediaDirectoryImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a directory of media files as follows:

## FiftyOneImageClassificationDataset [¶](\#fiftyoneimageclassificationdataset "Permalink to this headline")

The [`fiftyone.types.FiftyOneImageClassificationDataset`](../../api/fiftyone.types.html#fiftyone.types.FiftyOneImageClassificationDataset "fiftyone.types.FiftyOneImageClassificationDataset") type represents
a labeled dataset consisting of images and their associated classification
label(s) stored in a simple JSON format.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels.json

```

In the simplest case, `labels.json` can be a JSON file in the following format:

```python
{
    "classes": [\
        "<labelA>",\
        "<labelB>",\
        ...\
    ],
    "labels": {
        "<uuid1>": <target>,
        "<uuid2>": <target>,
        ...
    }
}

```

If the `classes` field is provided, the `target` values are class IDs that are
mapped to class label strings via `classes[target]`. If no `classes` field is
provided, then the `target` values directly store the label strings.

The target value in `labels` for unlabeled images is `None` (or missing).

The UUIDs can also be relative paths like `path/to/uuid`, in which case the
images in `data/` should be arranged in nested subfolders with the
corresponding names, or they can be absolute paths, in which case the images
may or may not be in `data/`.

Alternatively, `labels.json` can contain predictions with associated
confidences and additional attributes in the following format:

```python
{
    "classes": [\
        "<labelA>",\
        "<labelB>",\
        ...\
    ],
    "labels": {
        "<uuid1>": {
            "label": <target>,
            "confidence": <optional-confidence>,
            "attributes": {
                <optional-name>: <optional-value>,
                ...
            }
        },
        "<uuid2>": {
            "label": <target>,
            "confidence": <optional-confidence>,
            "attributes": {
                <optional-name>: <optional-value>,
                ...
            }
        },
        ...
    }
}

```

You can also load multilabel classifications in this format by storing lists
of targets in `labels.json`:

```python
{
    "classes": [\
        "<labelA>",\
        "<labelB>",\
        ...\
    ],
    "labels": {
        "<uuid1>": [<target1>, <target2>, ...],
        "<uuid2>": [<target1>, <target2>, ...],
        ...
    }
}

```

where the target values in `labels` can be class strings, class IDs, or dicts
in the format described above defining class labels, confidences, and optional
attributes.

Note

See [`FiftyOneImageClassificationDatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.FiftyOneImageClassificationDatasetImporter "fiftyone.utils.data.importers.FiftyOneImageClassificationDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an image classification dataset stored
in the above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

If the UUIDs in your labels are absolute paths to the source media, then
you can omit the `data_path` parameter from the example above.

## ImageClassificationDirectoryTree [¶](\#imageclassificationdirectorytree "Permalink to this headline")

The [`fiftyone.types.ImageClassificationDirectoryTree`](../../api/fiftyone.types.html#fiftyone.types.ImageClassificationDirectoryTree "fiftyone.types.ImageClassificationDirectoryTree") type represents a
directory tree whose subfolders define an image classification dataset.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    <classA>/
        <image1>.<ext>
        <image2>.<ext>
        ...
    <classB>/
        <image1>.<ext>
        <image2>.<ext>
        ...
    ...

```

Unlabeled images are stored in a subdirectory named `_unlabeled`.

Each class folder may contain nested subfolders of images.

Note

See [`ImageClassificationDirectoryTreeImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.ImageClassificationDirectoryTreeImporter "fiftyone.utils.data.importers.ImageClassificationDirectoryTreeImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an image classification directory tree
stored in the above format as follows:

## VideoClassificationDirectoryTree [¶](\#videoclassificationdirectorytree "Permalink to this headline")

The [`fiftyone.types.VideoClassificationDirectoryTree`](../../api/fiftyone.types.html#fiftyone.types.VideoClassificationDirectoryTree "fiftyone.types.VideoClassificationDirectoryTree") type represents a
directory tree whose subfolders define a video classification dataset.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    <classA>/
        <video1>.<ext>
        <video2>.<ext>
        ...
    <classB>/
        <video1>.<ext>
        <video2>.<ext>
        ...
    ...

```

Unlabeled videos are stored in a subdirectory named `_unlabeled`.

Each class folder may contain nested subfolders of videos.

Note

See [`VideoClassificationDirectoryTreeImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.VideoClassificationDirectoryTreeImporter "fiftyone.utils.data.importers.VideoClassificationDirectoryTreeImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a video classification directory tree
stored in the above format as follows:

## TFImageClassificationDataset [¶](\#tfimageclassificationdataset "Permalink to this headline")

The [`fiftyone.types.TFImageClassificationDataset`](../../api/fiftyone.types.html#fiftyone.types.TFImageClassificationDataset "fiftyone.types.TFImageClassificationDataset") type represents a
labeled dataset consisting of images and their associated classification labels
stored as
[TFRecords](https://www.tensorflow.org/tutorials/load_data/tfrecord).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    tf.records-?????-of-?????

```

where the features of the (possibly sharded) TFRecords are stored in the
following format:

```python
{
    # Image dimensions
    "height": tf.io.FixedLenFeature([], tf.int64),
    "width": tf.io.FixedLenFeature([], tf.int64),
    "depth": tf.io.FixedLenFeature([], tf.int64),
    # Image filename
    "filename": tf.io.FixedLenFeature([], tf.int64),
    # The image extension
    "format": tf.io.FixedLenFeature([], tf.string),
    # Encoded image bytes
    "image_bytes": tf.io.FixedLenFeature([], tf.string),
    # Class label string
    "label": tf.io.FixedLenFeature([], tf.string, default_value=""),
}

```

For unlabeled samples, the TFRecords do not contain `label` features.

Note

See `TFImageClassificationDatasetImporter`
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an image classification dataset stored
as a directory of TFRecords in the above format as follows:

Note

You can provide the `tf_records_path` argument instead of `dataset_dir` in
the examples above to directly specify the path to the TFRecord(s) to load.
See `TFImageClassificationDatasetImporter`
for details.

## FiftyOneImageDetectionDataset [¶](\#fiftyoneimagedetectiondataset "Permalink to this headline")

The [`fiftyone.types.FiftyOneImageDetectionDataset`](../../api/fiftyone.types.html#fiftyone.types.FiftyOneImageDetectionDataset "fiftyone.types.FiftyOneImageDetectionDataset") type represents a
labeled dataset consisting of images and their associated object detections
stored in a simple JSON format.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels.json

```

where `labels.json` is a JSON file in the following format:

```python
{
    "classes": [\
        <labelA>,\
        <labelB>,\
        ...\
    ],
    "labels": {
        <uuid1>: [\
            {\
                "label": <target>,\
                "bounding_box": [\
                    <top-left-x>, <top-left-y>, <width>, <height>\
                ],\
                "confidence": <optional-confidence>,\
                "attributes": {\
                    <optional-name>: <optional-value>,\
                    ...\
                }\
            },\
            ...\
        ],
        <uuid2>: [\
            ...\
        ],
        ...
    }
}

```

and where the bounding box coordinates are expressed as relative values in
`[0, 1] x [0, 1]`.

If the `classes` field is provided, the `target` values are class IDs that are
mapped to class label strings via `classes[target]`. If no `classes` field is
provided, then the `target` values directly store the label strings.

The target value in `labels` for unlabeled images is `None` (or missing).

The UUIDs can also be relative paths like `path/to/uuid`, in which case the
images in `data/` should be arranged in nested subfolders with the
corresponding names, or they can be absolute paths, in which case the images
may or may not be in `data/`.

Note

See [`FiftyOneImageDetectionDatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.FiftyOneImageDetectionDatasetImporter "fiftyone.utils.data.importers.FiftyOneImageDetectionDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an image detection dataset stored in the
above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

If the UUIDs in your labels are absolute paths to the source media, then
you can omit the `data_path` parameter from the example above.

## FiftyOneTemporalDetectionDataset [¶](\#fiftyonetemporaldetectiondataset "Permalink to this headline")

The [`fiftyone.types.FiftyOneTemporalDetectionDataset`](../../api/fiftyone.types.html#fiftyone.types.FiftyOneTemporalDetectionDataset "fiftyone.types.FiftyOneTemporalDetectionDataset") type represents a
labeled dataset consisting of videos and their associated temporal detections
stored in a simple JSON format.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels.json

```

where `labels.json` is a JSON file in the following format:

```python
{
    "classes": [\
        "<labelA>",\
        "<labelB>",\
        ...\
    ],
    "labels": {
        "<uuid1>": [\
            {\
                "label": <target>,\
                "support": [<first-frame>, <last-frame>],\
                "confidence": <optional-confidence>,\
                "attributes": {\
                    <optional-name>: <optional-value>,\
                    ...\
                }\
            },\
            {\
                "label": <target>,\
                "support": [<first-frame>, <last-frame>],\
                "confidence": <optional-confidence>,\
                "attributes": {\
                    <optional-name>: <optional-value>,\
                    ...\
                }\
            },\
            ...\
        ],
        "<uuid2>": [\
            {\
                "label": <target>,\
                "timestamps": [<start-timestamp>, <stop-timestamp>],\
                "confidence": <optional-confidence>,\
                "attributes": {\
                    <optional-name>: <optional-value>,\
                    ...\
                }\
            },\
            {\
                "label": <target>,\
                "timestamps": [<start-timestamp>, <stop-timestamp>],\
                "confidence": <optional-confidence>,\
                "attributes": {\
                    <optional-name>: <optional-value>,\
                    ...\
                }\
            },\
        ],
        ...
    }
}

```

The temporal range of each detection can be specified either via the `support`
key, which should contain the `[first, last]` frame numbers of the detection,
or the `timestamps` key, which should contain the `[start, stop]` timestamps of
the detection in seconds.

If the `classes` field is provided, the `target` values are class IDs that are
mapped to class label strings via `classes[target]`. If no `classes` field is
provided, then the `target` values directly store the label strings.

Unlabeled videos can have a `None` (or missing) key in `labels`.

The UUIDs can also be relative paths like `path/to/uuid`, in which case the
images in `data/` should be arranged in nested subfolders with the
corresponding names, or they can be absolute paths, in which case the images
may or may not be in `data/`.

Note

See [`FiftyOneTemporalDetectionDatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.FiftyOneTemporalDetectionDatasetImporter "fiftyone.utils.data.importers.FiftyOneTemporalDetectionDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a temporal detection dataset stored in
the above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

If the UUIDs in your labels are absolute paths to the source media, then
you can omit the `data_path` parameter from the example above.

## COCODetectionDataset [¶](\#cocodetectiondataset "Permalink to this headline")

The [`fiftyone.types.COCODetectionDataset`](../../api/fiftyone.types.html#fiftyone.types.COCODetectionDataset "fiftyone.types.COCODetectionDataset") type represents a labeled
dataset consisting of images and their associated object detections saved in
[COCO Object Detection Format](https://cocodataset.org/#format-data).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <filename0>.<ext>
        <filename1>.<ext>
        ...
    labels.json

```

where `labels.json` is a JSON file in the following format:

```python
{
    "info": {...},
    "licenses": [\
        {\
            "id": 1,\
            "name": "Attribution-NonCommercial-ShareAlike License",\
            "url": "http://creativecommons.org/licenses/by-nc-sa/2.0/",\
        },\
        ...\
    ],
    "categories": [\
        {\
            "id": 1,\
            "name": "cat",\
            "supercategory": "animal",\
            "keypoints": ["nose", "head", ...],\
            "skeleton": [[12, 14], [14, 16], ...]\
        },\
        ...\
    ],
    "images": [\
        {\
            "id": 1,\
            "license": 1,\
            "file_name": "<filename0>.<ext>",\
            "height": 480,\
            "width": 640,\
            "date_captured": null\
        },\
        ...\
    ],
    "annotations": [\
        {\
            "id": 1,\
            "image_id": 1,\
            "category_id": 1,\
            "bbox": [260, 177, 231, 199],\
            "segmentation": [...],\
            "keypoints": [224, 226, 2, ...],\
            "num_keypoints": 10,\
            "score": 0.95,\
            "area": 45969,\
            "iscrowd": 0\
        },\
        ...\
    ]
}

```

See [this page](https://cocodataset.org/#format-data) for a full
specification of the `segmentation` field.

For unlabeled datasets, `labels.json` does not contain an `annotations` field.

The `file_name` attribute of the labels file encodes the location of the
corresponding images, which can be any of the following:

- The filename of an image in the `data/` folder

- A relative path like `data/sub/folder/filename.ext` specifying the relative
path to the image in a nested subfolder of `data/`

- An absolute path to an image, which may or may not be in the `data/` folder

Note

See [`COCODetectionDatasetImporter`](../../api/fiftyone.utils.coco.html#fiftyone.utils.coco.COCODetectionDatasetImporter "fiftyone.utils.coco.COCODetectionDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a COCO detection dataset stored in the
above format as follows:

Note

By default, all supported label types are loaded (detections,
segmentations, and keypoints). However, you can choose specific type(s) to
load by passing the optional `label_types` argument to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir"):

```python
# Only load bounding boxes
dataset = fo.Dataset.from_dir(
    dataset_type=fo.types.COCODetectionDataset,
    label_types=["detections"],
    ...
)

```

See [`COCODetectionDatasetImporter`](../../api/fiftyone.utils.coco.html#fiftyone.utils.coco.COCODetectionDatasetImporter "fiftyone.utils.coco.COCODetectionDatasetImporter")
for complete documentation of the available COCO import options.

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

If the `file_name` key of your labels contains absolute paths to the source
media, then you can omit the `data_path` parameter from the example above.

If you have an existing dataset and corresponding model predictions stored in
COCO format, then you can use
[`add_coco_labels()`](../../api/fiftyone.utils.coco.html#fiftyone.utils.coco.add_coco_labels "fiftyone.utils.coco.add_coco_labels") to conveniently
add the labels to the dataset. The example below demonstrates a round-trip
export and then re-import of both images-and-labels and labels-only data in
COCO format:

```python
import fiftyone as fo
import fiftyone.zoo as foz
import fiftyone.utils.coco as fouc

dataset = foz.load_zoo_dataset("quickstart")
classes = dataset.distinct("predictions.detections.label")

# Export images and ground truth labels to disk
dataset.export(
    export_dir="/tmp/coco",
    dataset_type=fo.types.COCODetectionDataset,
    label_field="ground_truth",
    classes=classes,
)

# Export predictions
dataset.export(
    dataset_type=fo.types.COCODetectionDataset,
    labels_path="/tmp/coco/predictions.json",
    label_field="predictions",
    classes=classes,
)

# Now load ground truth labels into a new dataset
dataset2 = fo.Dataset.from_dir(
    dataset_dir="/tmp/coco",
    dataset_type=fo.types.COCODetectionDataset,
    label_field="ground_truth",
)

# And add model predictions
fouc.add_coco_labels(
    dataset2,
    "predictions",
    "/tmp/coco/predictions.json",
    classes,
)

# Verify that ground truth and predictions were imported as expected
print(dataset.count("ground_truth.detections"))
print(dataset2.count("ground_truth.detections"))
print(dataset.count("predictions.detections"))
print(dataset2.count("predictions.detections"))

```

Note

See [`add_coco_labels()`](../../api/fiftyone.utils.coco.html#fiftyone.utils.coco.add_coco_labels "fiftyone.utils.coco.add_coco_labels") for a
complete description of the available syntaxes for loading COCO-formatted
predictions to an existing dataset.

## VOCDetectionDataset [¶](\#vocdetectiondataset "Permalink to this headline")

The [`fiftyone.types.VOCDetectionDataset`](../../api/fiftyone.types.html#fiftyone.types.VOCDetectionDataset "fiftyone.types.VOCDetectionDataset") type represents a labeled
dataset consisting of images and their associated object detections saved in
[VOC format](http://host.robots.ox.ac.uk/pascal/VOC).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.xml
        <uuid2>.xml
        ...

```

where the labels XML files are in the following format:

```python
<annotation>
    <folder></folder>
    <filename>image.ext</filename>
    <path>/path/to/dataset-dir/data/image.ext</path>
    <source>
        <database></database>
    </source>
    <size>
        <width>640</width>
        <height>480</height>
        <depth>3</depth>
    </size>
    <segmented></segmented>
    <object>
        <name>cat</name>
        <pose></pose>
        <truncated>0</truncated>
        <difficult>0</difficult>
        <occluded>0</occluded>
        <bndbox>
            <xmin>256</xmin>
            <ymin>200</ymin>
            <xmax>450</xmax>
            <ymax>400</ymax>
        </bndbox>
    </object>
    <object>
        <name>dog</name>
        <pose></pose>
        <truncated>1</truncated>
        <difficult>1</difficult>
        <occluded>1</occluded>
        <bndbox>
            <xmin>128</xmin>
            <ymin>100</ymin>
            <xmax>350</xmax>
            <ymax>300</ymax>
        </bndbox>
    </object>
    ...
</annotation>

```

where either the `<filename>` and/or `<path>` field of the annotations may be
populated to specify the corresponding source image.

Unlabeled images have no corresponding file in `labels/`.

The `data/` and `labels/` files may contain nested subfolders of parallelly
organized images and masks.

Note

See [`VOCDetectionDatasetImporter`](../../api/fiftyone.utils.voc.html#fiftyone.utils.voc.VOCDetectionDatasetImporter "fiftyone.utils.voc.VOCDetectionDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a VOC detection dataset stored in the
above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

If the `<path>` field of your labels are populated with the absolute paths
to the source media, then you can omit the `data_path` parameter from the
example above.

## KITTIDetectionDataset [¶](\#kittidetectiondataset "Permalink to this headline")

The [`fiftyone.types.KITTIDetectionDataset`](../../api/fiftyone.types.html#fiftyone.types.KITTIDetectionDataset "fiftyone.types.KITTIDetectionDataset") type represents a labeled
dataset consisting of images and their associated object detections saved in
[KITTI format](http://www.cvlibs.net/datasets/kitti/eval_object.php).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.txt
        <uuid2>.txt
        ...

```

where the labels TXT files are space-delimited files where each row corresponds
to an object and the 15 (and optional 16th score) columns have the following
meanings:

| \# of<br>columns | Name | Description | Default |
| --- | --- | --- | --- |
| 1 | type | The object label |  |
| 1 | truncated | A float in `[0, 1]`, where 0 is non-truncated and<br>1 is fully truncated. Here, truncation refers to the object<br>leaving image boundaries | 0 |
| 1 | occluded | An int in `(0, 1, 2, 3)` indicating occlusion state,<br>where:- 0 = fully visible- 1 = partly occluded- 2 =<br>largely occluded- 3 = unknown | 0 |
| 1 | alpha | Observation angle of the object, in `[-pi, pi]` | 0 |
| 4 | bbox | 2D bounding box of object in the image in pixels, in the<br>format `[xtl, ytl, xbr, ybr]` |  |
| 1 | dimensions | 3D object dimensions, in meters, in the format<br>`[height, width, length]` | 0 |
| 1 | location | 3D object location `(x, y, z)` in camera coordinates<br>(in meters) | 0 |
| 1 | rotation\_y | Rotation around the y-axis in camera coordinates, in<br>`[-pi, pi]` | 0 |
| 1 | score | `(optional)` A float confidence for the detection |  |

When reading datasets of this type, all columns after the four `bbox` columns
are optional.

Unlabeled images have no corresponding file in `labels/`.

The `data/` and `labels/` files may contain nested subfolders of parallelly
organized images and masks.

Note

See [`KITTIDetectionDatasetImporter`](../../api/fiftyone.utils.kitti.html#fiftyone.utils.kitti.KITTIDetectionDatasetImporter "fiftyone.utils.kitti.KITTIDetectionDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a KITTI detection dataset stored in the
above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

## YOLOv4Dataset [¶](\#yolov4dataset "Permalink to this headline")

The [`fiftyone.types.YOLOv4Dataset`](../../api/fiftyone.types.html#fiftyone.types.YOLOv4Dataset "fiftyone.types.YOLOv4Dataset") type represents a labeled dataset
consisting of images and their associated object detections saved in
[YOLOv4 format](https://github.com/AlexeyAB/darknet).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    obj.names
    images.txt
    data/
        <uuid1>.<ext>
        <uuid1>.txt
        <uuid2>.<ext>
        <uuid2>.txt
        ...

```

where `obj.names` contains the object class labels:

```python
<label-0>
<label-1>
...

```

and `images.txt` contains the list of images in `data/`:

```python
data/<uuid1>.<ext>
data/<uuid2>.<ext>
...

```

The image paths in `images.txt` can be specified as either relative (to the
location of file) or as absolute paths. Alternatively, this file can be
omitted, in which case the `data/` directory is listed to determine the
available images.

The TXT files in `data/` are space-delimited files where each row corresponds
to an object in the image of the same name, in one of the following formats:

```python
# Detections
<target> <x-center> <y-center> <width> <height>
<target> <x-center> <y-center> <width> <height> <confidence>

# Polygons
<target> <x1> <y1> <x2> <y2> <x3> <y3> ...

```

where `<target>` is the zero-based integer index of the object class label from
`obj.names`, all coordinates are expressed as relative values in
`[0, 1] x [0, 1]`, and `<confidence>` is an optional confidence in `[0, 1]`.

Unlabeled images have no corresponding TXT file in `data/`.

The `data/` folder may contain nested subfolders.

Note

By default, all annotations are loaded as [`Detections`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.Detections "fiftyone.core.labels.Detections"), converting any
polylines to tight bounding boxes if necessary. However, you can choose to
load YOLO annotations as [`Polylines`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.Polylines "fiftyone.core.labels.Polylines") by passing the optional `label_type`
argument to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir"):

```python
# Load annotations as polygons
dataset = fo.Dataset.from_dir(
    dataset_type=fo.types.YOLOv4Dataset,
    label_type="polylines",
    ...
)

```

See [`YOLOv4DatasetImporter`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.YOLOv4DatasetImporter "fiftyone.utils.yolo.YOLOv4DatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a YOLOv4 dataset stored in the above
format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

If you have an existing dataset and corresponding model predictions stored in
YOLO format, then you can use
[`add_yolo_labels()`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.add_yolo_labels "fiftyone.utils.yolo.add_yolo_labels") to conveniently
add the labels to the dataset.

The example below demonstrates a round-trip export and then re-import of both
images-and-labels and labels-only data in YOLO format:

```python
import fiftyone as fo
import fiftyone.zoo as foz
import fiftyone.utils.yolo as fouy

dataset = foz.load_zoo_dataset("quickstart")
classes = dataset.distinct("predictions.detections.label")

# Export images and ground truth labels to disk
dataset.export(
    export_dir="/tmp/yolov4",
    dataset_type=fo.types.YOLOv4Dataset,
    label_field="ground_truth",
    classes=classes,
)

# Export predictions
dataset.export(
    dataset_type=fo.types.YOLOv4Dataset,
    labels_path="/tmp/yolov4/predictions",
    label_field="predictions",
    classes=classes,
)

# Now load ground truth labels into a new dataset
dataset2 = fo.Dataset.from_dir(
    dataset_dir="/tmp/yolov4",
    dataset_type=fo.types.YOLOv4Dataset,
    label_field="ground_truth",
)

# And add model predictions
fouy.add_yolo_labels(
    dataset2,
    "predictions",
    "/tmp/yolov4/predictions",
    classes,
)

# Verify that ground truth and predictions were imported as expected
print(dataset.count("ground_truth.detections"))
print(dataset2.count("ground_truth.detections"))
print(dataset.count("predictions.detections"))
print(dataset2.count("predictions.detections"))

```

Note

See [`add_yolo_labels()`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.add_yolo_labels "fiftyone.utils.yolo.add_yolo_labels") for a
complete description of the available syntaxes for loading YOLO-formatted
predictions to an existing dataset.

## YOLOv5Dataset [¶](\#yolov5dataset "Permalink to this headline")

The [`fiftyone.types.YOLOv5Dataset`](../../api/fiftyone.types.html#fiftyone.types.YOLOv5Dataset "fiftyone.types.YOLOv5Dataset") type represents a labeled dataset
consisting of images and their associated object detections saved in
[YOLOv5 format](https://github.com/ultralytics/yolov5).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    dataset.yaml
    images/
        train/
            <uuid1>.<ext>
            <uuid2>.<ext>
            ...
        val/
            <uuid3>.<ext>
            <uuid4>.<ext>
            ...
    labels/
        train/
            <uuid1>.txt
            <uuid2>.txt
            ...
        val/
            <uuid3>.txt
            <uuid4>.txt
            ...

```

where `dataset.yaml` contains the following information:

```python
path: <dataset_dir>  # optional
train: ./images/train/
val: ./images/val/

names:
  0: list
  1: of
  2: classes
  ...

```

See [this page](https://docs.ultralytics.com/datasets/detect) for a full
description of the possible format of `dataset.yaml`. In particular, the
dataset may contain one or more splits with arbitrary names, as the specific
split being imported or exported is specified by the `split` argument to
[`fiftyone.utils.yolo.YOLOv5DatasetImporter`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.YOLOv5DatasetImporter "fiftyone.utils.yolo.YOLOv5DatasetImporter"). Also, `dataset.yaml` can be
located outside of `<dataset_dir>` as long as the optional `path` is provided.

Note

Any relative paths in `dataset.yaml` or per-split TXT files are interpreted
relative to the directory containing these files, not your current working
directory.

The TXT files in `labels/` are space-delimited files where each row corresponds
to an object in the image of the same name, in one of the following formats:

```python
# Detections
<target> <x-center> <y-center> <width> <height>
<target> <x-center> <y-center> <width> <height> <confidence>

# Polygons
<target> <x1> <y1> <x2> <y2> <x3> <y3> ...

```

where `<target>` is the zero-based integer index of the object class label from
`names`, all coordinates are expressed as relative values in `[0, 1] x [0, 1]`,
and `<confidence>` is an optional confidence in `[0, 1]`.

Unlabeled images have no corresponding TXT file in `labels/`. The label file
path for each image is obtained by replacing `images/` with `labels/` in the
respective image path.

The image and labels directories for a given split may contain nested
subfolders of parallelly organized images and labels.

Note

By default, all annotations are loaded as [`Detections`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.Detections "fiftyone.core.labels.Detections"), converting any
polylines to tight bounding boxes if necessary. However, you can choose to
load YOLO annotations as [`Polylines`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.Polylines "fiftyone.core.labels.Polylines") by passing the optional `label_type`
argument to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir"):

```python
# Load annotations as polygons
dataset = fo.Dataset.from_dir(
    dataset_type=fo.types.YOLOv5Dataset,
    label_type="polylines",
    ...
)

```

See [`YOLOv5DatasetImporter`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.YOLOv5DatasetImporter "fiftyone.utils.yolo.YOLOv5DatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a YOLOv5 dataset stored in the above
format as follows:

```python
import fiftyone as fo

name = "my-dataset"
dataset_dir = "/path/to/yolov5-dataset"

# The splits to load
splits = ["train", "val"]

# Load the dataset, using tags to mark the samples in each split
dataset = fo.Dataset(name)
for split in splits:
    dataset.add_dir(
        dataset_dir=dataset_dir,
        dataset_type=fo.types.YOLOv5Dataset,
        split=split,
        tags=split,
)

# View summary info about the dataset
print(dataset)

# Print the first few samples in the dataset
print(dataset.head())

```

If you have an existing dataset and corresponding model predictions stored in
YOLO format, then you can use
[`add_yolo_labels()`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.add_yolo_labels "fiftyone.utils.yolo.add_yolo_labels") to conveniently
add the labels to the dataset.

The example below demonstrates a round-trip export and then re-import of both
images-and-labels and labels-only data in YOLO format:

```python
import fiftyone as fo
import fiftyone.zoo as foz
import fiftyone.utils.yolo as fouy

dataset = foz.load_zoo_dataset("quickstart")
classes = dataset.distinct("predictions.detections.label")

# YOLOv5 format supports splits, so let's grab only the `validation` split
view = dataset.match_tags("validation")

# Export images and ground truth labels to disk
view.export(
    export_dir="/tmp/yolov5",
    dataset_type=fo.types.YOLOv5Dataset,
    split="validation",
    label_field="ground_truth",
    classes=classes,
)

# Export predictions
view.export(
    dataset_type=fo.types.YOLOv5Dataset,
    labels_path="/tmp/yolov5/predictions/validation",
    label_field="predictions",
    classes=classes,
)

# Now load ground truth labels into a new dataset
dataset2 = fo.Dataset.from_dir(
    dataset_dir="/tmp/yolov5",
    dataset_type=fo.types.YOLOv5Dataset,
    split="validation",
    label_field="ground_truth",
)

# And add model predictions
fouy.add_yolo_labels(
    dataset2,
    "predictions",
    "/tmp/yolov5/predictions/validation",
    classes,
)

# Verify that ground truth and predictions were imported as expected
print(view.count("ground_truth.detections"))
print(dataset2.count("ground_truth.detections"))
print(view.count("predictions.detections"))
print(dataset2.count("predictions.detections"))

```

Note

See [`add_yolo_labels()`](../../api/fiftyone.utils.yolo.html#fiftyone.utils.yolo.add_yolo_labels "fiftyone.utils.yolo.add_yolo_labels") for a
complete description of the available syntaxes for loading YOLO-formatted
predictions to an existing dataset.

## TFObjectDetectionDataset [¶](\#tfobjectdetectiondataset "Permalink to this headline")

The [`fiftyone.types.TFObjectDetectionDataset`](../../api/fiftyone.types.html#fiftyone.types.TFObjectDetectionDataset "fiftyone.types.TFObjectDetectionDataset") type represents a labeled
dataset consisting of images and their associated object detections stored as
[TFRecords](https://www.tensorflow.org/tutorials/load_data/tfrecord) in
[TF Object Detection API format](https://github.com/tensorflow/models/blob/master/research/object_detection).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    tf.records-?????-of-?????

```

where the features of the (possibly sharded) TFRecords are stored in the
following format:

```python
{
    # Image dimensions
    "image/height": tf.io.FixedLenFeature([], tf.int64),
    "image/width": tf.io.FixedLenFeature([], tf.int64),

    # Image filename is used for both of these when writing
    "image/filename": tf.io.FixedLenFeature([], tf.string),
    "image/source_id": tf.io.FixedLenFeature([], tf.string),

    # Encoded image bytes
    "image/encoded": tf.io.FixedLenFeature([], tf.string),

    # Image format, either `jpeg` or `png`
    "image/format": tf.io.FixedLenFeature([], tf.string),

    # Normalized bounding box coordinates in `[0, 1]`
    "image/object/bbox/xmin": tf.io.FixedLenSequenceFeature(
        [], tf.float32, allow_missing=True
    ),
    "image/object/bbox/xmax": tf.io.FixedLenSequenceFeature(
        [], tf.float32, allow_missing=True
    ),
    "image/object/bbox/ymin": tf.io.FixedLenSequenceFeature(
        [], tf.float32, allow_missing=True
    ),
    "image/object/bbox/ymax": tf.io.FixedLenSequenceFeature(
        [], tf.float32, allow_missing=True
    ),

    # Class label string
    "image/object/class/text": tf.io.FixedLenSequenceFeature(
        [], tf.string, allow_missing=True
    ),

    # Integer class ID
    "image/object/class/label": tf.io.FixedLenSequenceFeature(
        [], tf.int64, allow_missing=True
    ),
}

```

The TFRecords for unlabeled samples do not contain `image/object/*` features.

Note

See `TFObjectDetectionDatasetImporter`
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an object detection dataset stored as a
directory of TFRecords in the above format as follows:

Note

You can provide the `tf_records_path` argument instead of `dataset_dir` in
the examples above to directly specify the path to the TFRecord(s) to load.
See `TFObjectDetectionDatasetImporter`
for details.

## ImageSegmentationDirectory [¶](\#imagesegmentationdirectory "Permalink to this headline")

The [`fiftyone.types.ImageSegmentationDirectory`](../../api/fiftyone.types.html#fiftyone.types.ImageSegmentationDirectory "fiftyone.types.ImageSegmentationDirectory") type represents a
labeled dataset consisting of images and their associated semantic
segmentations stored as images on disk.

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <filename1>.<ext>
        <filename2>.<ext>
        ...
    labels/
        <filename1>.<ext>
        <filename2>.<ext>
        ...

```

where `labels/` contains the semantic segmentations stored as images.

Unlabeled images have no corresponding file in `labels/`.

The `data/` and `labels/` files may contain nested subfolders of parallelly
organized images and masks.

Note

See [`ImageSegmentationDirectoryImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.ImageSegmentationDirectoryImporter "fiftyone.utils.data.importers.ImageSegmentationDirectoryImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an image segmentation dataset stored in
the above format as follows:

You can also independently specify the locations of the masks and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

## CVATImageDataset [¶](\#cvatimagedataset "Permalink to this headline")

The [`fiftyone.types.CVATImageDataset`](../../api/fiftyone.types.html#fiftyone.types.CVATImageDataset "fiftyone.types.CVATImageDataset") type represents a labeled dataset
consisting of images and their associated tags and object detections stored in
[CVAT image format](https://github.com/opencv/cvat).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels.xml

```

where `labels.xml` is an XML file in the following format:

```python
<?xml version="1.0" encoding="utf-8"?>
<annotations>
    <version>1.1</version>
    <meta>
        <task>
            <id>0</id>
            <name>task-name</name>
            <size>51</size>
            <mode>annotation</mode>
            <overlap></overlap>
            <bugtracker></bugtracker>
            <flipped>False</flipped>
            <created>2017-11-20 11:51:51.000000+00:00</created>
            <updated>2017-11-20 11:51:51.000000+00:00</updated>
            <labels>
                <label>
                    <name>car</name>
                    <attributes>
                        <attribute>
                            <name>type</name>
                            <values>coupe\\nsedan\\ntruck</values>
                        </attribute>
                        ...
                    </attributes>
                </label>
                <label>
                    <name>traffic_line</name>
                    <attributes>
                        <attribute>
                            <name>color</name>
                            <values>white\\nyellow</values>
                        </attribute>
                        ...
                    </attributes>
                </label>
                ...
            </labels>
        </task>
        <segments>
            <segment>
                <id>0</id>
                <start>0</start>
                <stop>50</stop>
                <url></url>
            </segment>
        </segments>
        <owner>
            <username></username>
            <email></email>
        </owner>
        <dumped>2017-11-20 11:51:51.000000+00:00</dumped>
    </meta>
    <image id="0" name="<uuid1>.<ext>" width="640" height="480">
        <tag label="urban"></tag>
        ...
        <box label="car" xtl="100" ytl="50" xbr="325" ybr="190" occluded="0">
            <attribute name="type">sedan</attribute>
            ...
        </box>
        ...
        <polygon label="car" points="561.30,916.23;561.30,842.77;...;560.20,966.67" occluded="0">
            <attribute name="make">Honda</attribute>
            ...
        </polygon>
        ...
        <polyline label="traffic_line" points="462.10,0.00;126.80,1200.00" occluded="0">
            <attribute name="color">yellow</attribute>
            ...
        </polyline>
        ...
        <points label="wheel" points="574.90,939.48;1170.16,907.90;...;600.16,459.48" occluded="0">
            <attribute name="location">front_driver_side</attribute>
            ...
        </points>
        ...
    </image>
    ...
    <image id="50" name="<uuid51>.<ext>" width="640" height="480">
        ...
    </image>
</annotations>

```

Unlabeled images have no corresponding `image` tag in `labels.xml`.

The `name` field of the `<image>` tags in the labels file encodes the location
of the corresponding images, which can be any of the following:

- The filename of an image in the `data/` folder

- A relative path like `data/sub/folder/filename.ext` specifying the relative
path to the image in a nested subfolder of `data/`

- An absolute path to an image, which may or may not be in the `data/` folder

Note

See [`CVATImageDatasetImporter`](../../api/fiftyone.utils.cvat.html#fiftyone.utils.cvat.CVATImageDatasetImporter "fiftyone.utils.cvat.CVATImageDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a CVAT image dataset stored in the above
format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

If the `name` key of your labels contains absolute paths to the source
media, then you can omit the `data_path` parameter from the example above.

## CVATVideoDataset [¶](\#cvatvideodataset "Permalink to this headline")

The [`fiftyone.types.CVATVideoDataset`](../../api/fiftyone.types.html#fiftyone.types.CVATVideoDataset "fiftyone.types.CVATVideoDataset") type represents a labeled dataset
consisting of videos and their associated object detections stored in
[CVAT video format](https://github.com/opencv/cvat).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.xml
        <uuid2>.xml
        ...

```

where the labels XML files are stored in the following format:

```python
<?xml version="1.0" encoding="utf-8"?>
<annotations>
    <version>1.1</version>
    <meta>
        <task>
            <id>task-id</id>
            <name>task-name</name>
            <size>51</size>
            <mode>interpolation</mode>
            <overlap></overlap>
            <bugtracker></bugtracker>
            <flipped>False</flipped>
            <created>2017-11-20 11:51:51.000000+00:00</created>
            <updated>2017-11-20 11:51:51.000000+00:00</updated>
            <labels>
                <label>
                    <name>car</name>
                    <attributes>
                        <attribute>
                            <name>type</name>
                            <values>coupe\\nsedan\\ntruck</values>
                        </attribute>
                        ...
                    </attributes>
                </label>
                <label>
                    <name>traffic_line</name>
                    <attributes>
                        <attribute>
                            <name>color</name>
                            <values>white\\nyellow</values>
                        </attribute>
                        ...
                    </attributes>
                </label>
                ...
            </labels>
        </task>
        <segments>
            <segment>
                <id>0</id>
                <start>0</start>
                <stop>50</stop>
                <url></url>
            </segment>
        </segments>
        <owner>
            <username></username>
            <email></email>
        </owner>
        <original_size>
            <width>640</width>
            <height>480</height>
        </original_size>
        <dumped>2017-11-20 11:51:51.000000+00:00</dumped>
    </meta>
    <track id="0" label="car">
        <box frame="0" xtl="100" ytl="50" xbr="325" ybr="190" outside="0" occluded="0" keyframe="1">
            <attribute name="type">sedan</attribute>
            ...
        </box>
        ...
    </track>
    <track id="1" label="car">
        <polygon frame="0" points="561.30,916.23;561.30,842.77;...;560.20,966.67" outside="0" occluded="0" keyframe="1">
            <attribute name="make">Honda</attribute>
            ...
        </polygon>
        ...
    </track>
    ...
    <track id="10" label="traffic_line">
        <polyline frame="10" points="462.10,0.00;126.80,1200.00" outside="0" occluded="0" keyframe="1">
            <attribute name="color">yellow</attribute>
            ...
        </polyline>
        ...
    </track>
    ...
    <track id="88" label="wheel">
        <points frame="176" points="574.90,939.48;1170.16,907.90;...;600.16,459.48" outside="0" occluded="0" keyframe="1">
            <attribute name="location">front_driver_side</attribute>
            ...
        </points>
        ...
    </track>
</annotations>

```

Unlabeled videos have no corresponding file in `labels/`.

The `data/` and `labels/` files may contain nested subfolders of parallelly
organized images and labels.

Note

See [`CVATVideoDatasetImporter`](../../api/fiftyone.utils.cvat.html#fiftyone.utils.cvat.CVATVideoDatasetImporter "fiftyone.utils.cvat.CVATVideoDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a CVAT video dataset stored in the above
format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

## OpenLABELImageDataset [¶](\#openlabelimagedataset "Permalink to this headline")

The [`fiftyone.types.OpenLABELImageDataset`](../../api/fiftyone.types.html#fiftyone.types.OpenLABELImageDataset "fiftyone.types.OpenLABELImageDataset") type represents a labeled
dataset consisting of images and their associated multitask predictions stored =
in [OpenLABEL format](https://www.asam.net/index.php?eID=dumpFile&t=f&f=3876&token=413e8c85031ae64cc35cf42d0768627514868b2f).

OpenLABEL is a flexible format which allows labels to be stored in a variety of
different ways with respect to the corresponding media files. The following
enumerates the possible structures in which media data and OpenLABEL formatted
label files can be stored in ways that is understood by FiftyOne:

1. One label file per image. Each label contains only the metadata and labels
associated with the image of the same name. In this case, the `labels_path`
argument is expected to be a directory, if provided:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.json
        <uuid2>.json
        ...

```

2. One label file for all images. The label file contains all of the metadata
and labels associated with every image. In this case, there needs to be
additional information provided in the label file to match labels to
images. Specifically, the image filepath corresponding to a label must be
stored as a stream:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels.json

```

3. Multiple label files, each corresponding to one or more images. This case is
similar to when there is a single label file, except that the label
information may be spread out over multiple files. Since the filenames
cannot be used to match labels to images, the image filepaths must again be
stored as streams in the labels files:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <labels-filename1>.json
        <labels-filename2>.json
        ...

```

As for the actual structure of the labels files themselves, labels are stored
in one or more JSON files and can follow a variety of formats. In general
following this format:

Note

All object information stored in the `frames` key is applied to the
corresponding image.

```python
{
    "openlabel": {
        "metadata": {
            "schema_version": "1.0.0",
            "uri": "/path/to/<uuid>.<ext>",
        },
        "objects": {
            "object_uuid1": {
                "name": "instance1",
                "type": "label1",
                "object_data": {
                    "bbox": [\
                        {\
                            "name": "shape",\
                            "val": [\
                                center-x,\
                                center-y,\
                                width,\
                                height\
                            ]\
                        }\
                    ]
                }
            },
            "object_uuid2": {
                "name": "instance1",
                "type": "label2",
                "object_data": {},  # DEFINED IN FRAMES
            }
        },
        "frames": {
            "0": {
               "frame_properties": {
                  "streams": {
                     "Camera1": {
                        "uri": "<uuid>.<ext>"
                     }
                  }
               },
               "objects": {
                  "object_uuid2": {
                     "object_data": {
                        "poly2d": [\
                           {\
                              "attributes": {\
                                 "boolean": [\
                                    {\
                                       "name": "is_hole",\
                                       "val": false\
                                    }\
                                 ],\
                                 "text": [\
                                    {  # IF NOT PROVIDED OTHERWISE\
                                       "name": "stream",\
                                       "val": "Camera1"\
                                    }\
                                 ]\
                              },\
                              "closed": true,\
                              "mode": "MODE_POLY2D_ABSOLUTE",\
                              "name": "polygon_name",\
                              "stream": "Camera1",  # IF NOT IN ATTRIBUTES\
                              "val": [\
                                 point1-x,\
                                 point1-y,\
                                 point2-x,\
                                 point2-y,\
                                 ...\
                              ]\
                           }\
                        ]
                     }
                  }
              }
           }
        },
        "streams": {
           "Camera1": {
              "description": "",
              "stream_properties": {
                 "height": 480,
                 "width": 640
              },
              "type": "camera"
           }
        },
        "ontologies": ... # NOT PARSED
        "relations": ... # NOT PARSED
        "resources": ... # NOT PARSED
        "tags": ... # NOT PARSED
    }
}

```

Note

See [`OpenLABELImageDatasetImporter`](../../api/fiftyone.utils.openlabel.html#fiftyone.utils.openlabel.OpenLABELImageDatasetImporter "fiftyone.utils.openlabel.OpenLABELImageDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

If loading [`Keypoints`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.Keypoints "fiftyone.core.labels.Keypoints") related to a given [`KeypointSkeleton`](../../api/fiftyone.core.odm.dataset.html#fiftyone.core.odm.dataset.KeypointSkeleton "fiftyone.core.odm.dataset.KeypointSkeleton"), then you can
provide a `skeleton` and `skeleton_key` argument to the
[`OpenLABELImageDatasetImporter`](../../api/fiftyone.utils.openlabel.html#fiftyone.utils.openlabel.OpenLABELImageDatasetImporter "fiftyone.utils.openlabel.OpenLABELImageDatasetImporter")
allowing you to match points in your annotations file to labels in the
[`KeypointSkeleton`](../../api/fiftyone.core.odm.dataset.html#fiftyone.core.odm.dataset.KeypointSkeleton "fiftyone.core.odm.dataset.KeypointSkeleton") and load the points and their attributes in the correct
order.

You can create a FiftyOne dataset from a OpenLABEL image dataset stored in the
above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

OpenLABEL is a flexible format that allows for many user-specific
decisions about how to represent labels and metadata. If you have
OpenLABEL-compliant data in a format not understood by the current
importers, please make an issue or contribute a pull request!

## OpenLABELVideoDataset [¶](\#openlabelvideodataset "Permalink to this headline")

The [`fiftyone.types.OpenLABELVideoDataset`](../../api/fiftyone.types.html#fiftyone.types.OpenLABELVideoDataset "fiftyone.types.OpenLABELVideoDataset") type represents a labeled
dataset consisting of videos and their associated multitask predictions stored
in [OpenLABEL format](https://www.asam.net/index.php?eID=dumpFile&t=f&f=3876&token=413e8c85031ae64cc35cf42d0768627514868b2f).

OpenLABEL is a flexible format which allows labels to be stored in a variety of
different ways with respect to the corresponding media files. The following
enumerates the possible structures in which media data and OpenLABEL formatted
label files can be stored in ways that is understood by FiftyOne:

1. One label file per video. Each label contains only the metadata and labels
associated with the video of the same name. In this case, the `labels_path`
argument is expected to be a directory, if provided:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.json
        <uuid2>.json
        ...

```

2. One label file for all videos. The label file contains all of the metadata
and labels associated with every video. In this case, there needs to be
additional information provided in the label file to match labels to
videos. Specifically, the video filepath corresponding to a label must be
stored as a stream:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels.json

```

3. Multiple label files, each corresponding to one or more videos. This case is
similar to when there is a single label file, except that the label
information may be spread out over multiple files. Since the filenames
cannot be used to match labels to videos, the video filepaths must again be
stored as streams in the labels files:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <labaels-filename1>.json
        <labaels-filename2>.json
        ...

```

As for the actual structure of the labels files themselves, labels are stored
in one or more JSON files and can follow a variety of formats. In general
following this format:

```python
{
    "openlabel": {
        "metadata": {
            "schema_version": "1.0.0",
            "uri": "/path/to/<uuid>.<ext>",
        },
        "objects": {
            "object_uuid1": {
                "name": "instance1",
                "type": "label1",
                "object_data": {
                    "bbox": [\
                        {\
                            "name": "shape",\
                            "val": [\
                                center-x,\
                                center-y,\
                                width,\
                                height\
                            ]\
                        }\
                    ]
                }
                "frame_intervals": [{"frame_start": 0, "frame_end": 10}],
            },
            "object_uuid2": {
                "name": "instance1",
                "type": "label2",
                "object_data": {},  # DEFINED IN FRAMES
            }
        },
        "frames": {
            "0": {
               "frame_properties": {
                  "streams": {
                     "Camera1": {
                        "uri":"<uuid>.<ext>"
                     }
                  }
               },
               "objects": {
                  "object_uuid2": {
                     "object_data": {
                        "poly2d": [\
                           {\
                              "attributes": {\
                                 "boolean": [\
                                    {\
                                       "name": "is_hole",\
                                       "val": false\
                                    }\
                                 ],\
                                 "text": [\
                                    {  # IF NOT PROVIDED OTHERWISE\
                                       "name": "stream",\
                                       "val": "Camera1"\
                                    }\
                                 ]\
                              },\
                              "closed": true,\
                              "mode": "MODE_POLY2D_ABSOLUTE",\
                              "name": "polygon_name",\
                              "stream": "Camera1",  # IF NOT IN ATTRIBUTES\
                              "val": [\
                                 point1-x,\
                                 point1-y,\
                                 point2-x,\
                                 point2-y,\
                                 ...\
                              ]\
                           }\
                        ]
                     }
                  }
              },
              ...
           }
        },
        "streams": {
           "Camera1": {
              "description": "",
              "stream_properties": {
                 "height": 480,
                 "width": 640
              },
              "type": "camera"
           }
        },
        "ontologies": ...  # NOT PARSED
        "relations" ...  # NOT PARSED
        "resources" ...  # NOT PARSED
        "tags": ...  # NOT PARSED
    }
}

```

Note

See [`OpenLABELVideoDatasetImporter`](../../api/fiftyone.utils.openlabel.html#fiftyone.utils.openlabel.OpenLABELVideoDatasetImporter "fiftyone.utils.openlabel.OpenLABELVideoDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

If loading [`Keypoints`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.Keypoints "fiftyone.core.labels.Keypoints") related to a given [`KeypointSkeleton`](../../api/fiftyone.core.odm.dataset.html#fiftyone.core.odm.dataset.KeypointSkeleton "fiftyone.core.odm.dataset.KeypointSkeleton"), then you can
provide a `skeleton` and `skeleton_key` argument to the
[`OpenLABELVideoDatasetImporter`](../../api/fiftyone.utils.openlabel.html#fiftyone.utils.openlabel.OpenLABELVideoDatasetImporter "fiftyone.utils.openlabel.OpenLABELVideoDatasetImporter")
allowing you to match points in your annotations file to labels in the
[`KeypointSkeleton`](../../api/fiftyone.core.odm.dataset.html#fiftyone.core.odm.dataset.KeypointSkeleton "fiftyone.core.odm.dataset.KeypointSkeleton") and load the points and their attributes in the correct
order.

You can create a FiftyOne dataset from a OpenLABEL video dataset stored in the
above format as follows:

You can also independently specify the locations of the labels and the root
directory containing the corresponding media files by providing the
`labels_path` and `data_path` parameters rather than `dataset_dir`:

Note

OpenLABEL is a flexible format that allows for many user-specific
decisions about how to represent labels and metadata. If you have
OpenLABEL-compliant data in a format not understood by the current
importers, please make an issue or contribute a pull request!

## FiftyOneImageLabelsDataset [¶](\#fiftyoneimagelabelsdataset "Permalink to this headline")

The [`fiftyone.types.FiftyOneImageLabelsDataset`](../../api/fiftyone.types.html#fiftyone.types.FiftyOneImageLabelsDataset "fiftyone.types.FiftyOneImageLabelsDataset") type represents a
labeled dataset consisting of images and their associated multitask predictions
stored in
[ETA ImageLabels format](https://github.com/voxel51/eta/blob/develop/docs/image_labels_guide.md).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.json
        <uuid2>.json
        ...
    manifest.json

```

where `manifest.json` is a JSON file in the following format:

```python
{
    "type": "eta.core.datasets.LabeledImageDataset",
    "description": "",
    "index": [\
        {\
            "data": "data/<uuid1>.<ext>",\
            "labels": "labels/<uuid1>.json"\
        },\
        {\
            "data": "data/<uuid2>.<ext>",\
            "labels": "labels/<uuid2>.json"\
        },\
        ...\
    ]
}

```

and where each labels JSON file is stored in
[ETA ImageLabels format](https://github.com/voxel51/eta/blob/develop/docs/image_labels_guide.md).

For unlabeled images, an empty `eta.core.image.ImageLabels` file is stored.

Note

See [`FiftyOneImageLabelsDatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.FiftyOneImageLabelsDatasetImporter "fiftyone.utils.data.importers.FiftyOneImageLabelsDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from an image labels dataset stored in the
above format as follows:

## FiftyOneVideoLabelsDataset [¶](\#fiftyonevideolabelsdataset "Permalink to this headline")

The [`fiftyone.types.FiftyOneVideoLabelsDataset`](../../api/fiftyone.types.html#fiftyone.types.FiftyOneVideoLabelsDataset "fiftyone.types.FiftyOneVideoLabelsDataset") type represents a
labeled dataset consisting of videos and their associated labels stored in
[ETA VideoLabels format](https://github.com/voxel51/eta/blob/develop/docs/video_labels_guide.md).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <uuid1>.<ext>
        <uuid2>.<ext>
        ...
    labels/
        <uuid1>.json
        <uuid2>.json
        ...
    manifest.json

```

where `manifest.json` is a JSON file in the following format:

```python
{
    "type": "eta.core.datasets.LabeledVideoDataset",
    "description": "",
    "index": [\
        {\
            "data": "data/<uuid1>.<ext>",\
            "labels": "labels/<uuid1>.json"\
        },\
        {\
            "data": "data/<uuid2>.<ext>",\
            "labels": "labels/<uuid2>.json"\
        },\
        ...\
    ]
}

```

and where each labels JSON file is stored in
[ETA VideoLabels format](https://github.com/voxel51/eta/blob/develop/docs/video_labels_guide.md).

For unlabeled videos, an empty `eta.core.video.VideoLabels` file is written.

Note

See [`FiftyOneVideoLabelsDatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.FiftyOneVideoLabelsDatasetImporter "fiftyone.utils.data.importers.FiftyOneVideoLabelsDatasetImporter")
for parameters that can be passed to methods like
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to
customize the import of datasets of this type.

You can create a FiftyOne dataset from a video labels dataset stored in the
above format as follows:

## BDDDataset [¶](\#bdddataset "Permalink to this headline")

The [`fiftyone.types.BDDDataset`](../../api/fiftyone.types.html#fiftyone.types.BDDDataset "fiftyone.types.BDDDataset") type represents a labeled dataset
consisting of images and their associated multitask predictions saved in
[Berkeley DeepDrive (BDD) format](https://bdd-data.berkeley.edu).

Datasets of this type are read in the following format:

```python
<dataset_dir>/
    data/
        <filename0>.<ext>
        <filename1>.<ext>
        ...
    labels.json

```

where `labels.json` is a JSON file in the following format:

```python
[\
    {\
        "name": "<filename0>.<ext>",\
        "attributes": {\
            "scene": "city street",\
            "timeofday": "daytime",\
            "weather": "overcast"\
        },\
        "labels": [\
            {\
                "id": 0,\
                "category": "traffic sign",\
                "manualAttributes": true,\
                "manualShape": true,\
                "attributes": {\
                    "occluded": false,\
                    "trafficLightColor": "none",\
                    "truncated": false\
                },\
                "box2d": {\
                    "x1": 1000.698742,\
                    "x2": 1040.626872,\
                    "y1": 281.992415,\
                    "y2": 326.91156\
                },\
                "score": 0.95\
            },\
            ...\
            {\
                "id": 34,\
                "category": "drivable area",\
                "manualAttributes": true,\
                "manualShape": true,\
                "attributes": {\
                    "areaType": "direct"\
                },\
                "poly2d": [\
                    {\
                        "types": "LLLLCCC",\
                        "closed": true,\
                        "vertices": [\
                            [241.143645, 697.923453],\
                            [541.525255, 380.564983],\
                            ...\
                        ]\
                    }\
                ],\
                "score": 0.87\
            },\
            ...\
            {\
                "id": 109356,\
                "category": "lane",\
                "attributes": {\
                    "laneDirection": "parallel",\
                    "laneStyle": "dashed",\
                    "laneType": "single white"\
                },\
                "manualShape": true,\
                "manualAttributes": true,\
                "poly2d": [\
                    {\
                        "types": "LL",\
                        "closed": false,\
                        "vertices": [\
                            [492.879546, 331.939543],\
                            [0, 471.076658],\
                            ...\
                        ]\
                    }\
                ],\
                "score": 0.98\
            },\
            ...\
        }\
    }\
    ...\
]\
\
```\
\
Unlabeled images have no corresponding entry in `labels.json`.\
\
The `name` attribute of the labels file encodes the location of the\
corresponding images, which can be any of the following:\
\
- The filename of an image in the `data/` folder\
\
- A relative path like `data/sub/folder/filename.ext` specifying the relative\
path to the image in a nested subfolder of `data/`\
\
- An absolute path to an image, which may or may not be in the `data/` folder\
\
\
Note\
\
See [`BDDDatasetImporter`](../../api/fiftyone.utils.bdd.html#fiftyone.utils.bdd.BDDDatasetImporter "fiftyone.utils.bdd.BDDDatasetImporter")\
for parameters that can be passed to methods like\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to\
customize the import of datasets of this type.\
\
You can create a FiftyOne dataset from a BDD dataset stored in the above format\
as follows:\
\
You can also independently specify the locations of the labels and the root\
directory containing the corresponding media files by providing the\
`labels_path` and `data_path` parameters rather than `dataset_dir`:\
\
Note\
\
If the `name` key of your labels contains absolute paths to the source\
media, then you can omit the `data_path` parameter from the example above.\
\
## CSVDataset [¶](\#csvdataset "Permalink to this headline")\
\
The [`fiftyone.types.CSVDataset`](../../api/fiftyone.types.html#fiftyone.types.CSVDataset "fiftyone.types.CSVDataset") type represents a dataset consisting\
of images or videos and their associated field values stored as columns of a\
CSV file.\
\
Datasets of this type are read in the following format:\
\
```\
<dataset_dir>/\
    data/\
        <filename1>.<ext>\
        <filename2>.<ext>\
        ...\
    labels.csv\
\
```\
\
where `labels.csv` is a CSV file in the following format:\
\
```\
field1,field2,field3,...\
value1,value2,value3,...\
value1,value2,value3,...\
...\
\
```\
\
One sample will be generated per row in the CSV file (excluding the header\
row).\
\
One column of the CSV file must contain media paths, which may be either:\
\
- filenames or relative paths to media files in `data/`\
\
- absolute paths to media files\
\
\
By default it is assumed that a `filepath` column exists and contains the\
media paths, but you can customize this via the optional `media_field`\
parameter.\
\
By default all columns are loaded as string fields, but you can provide the\
optional `fields` parameter to select a subset of columns to load or provide\
custom parsing functions for each field, as demonstrated below.\
\
Note\
\
See [`CSVDatasetImporter`](../../api/fiftyone.utils.csv.html#fiftyone.utils.csv.CSVDatasetImporter "fiftyone.utils.csv.CSVDatasetImporter")\
for parameters that can be passed to methods like\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to\
customize the import of datasets of this type.\
\
You can create a FiftyOne dataset from a CSV dataset stored in the above\
format as follows:\
\
If your CSV file contains absolute media paths, then you can directly specify\
the path to the CSV file itself by providing the `labels_path` parameter.\
\
Additionally, you can use the `fields` parameter to customize how each field is\
parsed, as demonstrated below:\
\
## DICOMDataset [¶](\#dicomdataset "Permalink to this headline")\
\
The [`fiftyone.types.DICOMDataset`](../../api/fiftyone.types.html#fiftyone.types.DICOMDataset "fiftyone.types.DICOMDataset") type represents a dataset consisting\
of images and their associated properties stored in\
[DICOM format](https://en.wikipedia.org/wiki/DICOM).\
\
Note\
\
You must have [pydicom<3](https://github.com/pydicom/pydicom) installed\
in order to load DICOM datasets.\
\
The standard format for datasets of this type is the following:\
\
```\
<dataset_dir>/\
    <filename1>.dcm\
    <filename2>.dcm\
\
```\
\
where each `.dcm` file is a DICOM file that can be read via\
[`pydicom.dcmread`](https://pydicom.github.io/pydicom/stable/reference/generated/pydicom.filereader.dcmread.html#pydicom.filereader.dcmread "(in pydicom v3.0.1)").\
\
Alternatively, rather than providing a `dataset_dir`, you can provide the\
`dicom_path` argument, which can directly specify a glob pattern of DICOM\
files or the path to a\
[DICOMDIR](https://pydicom.github.io/pydicom/stable/tutorials/filesets.html)\
file.\
\
By default, all attributes in the DICOM files discoverable via\
[`pydicom.dataset.Dataset.dir()`](https://pydicom.github.io/pydicom/stable/reference/generated/pydicom.dataset.Dataset.html#pydicom.dataset.Dataset.dir "(in pydicom v3.0.1)") with supported types are loaded\
into sample-level fields, but you can select only specific attributes by\
passing the optional `keywords` argument.\
\
Note\
\
When importing DICOM datasets, the pixel data are converted to 8-bit\
images, using the `SmallestImagePixelValue` and\
`LargestImagePixelValue` attributes (if present), to inform the\
conversion.\
\
The images are written to a backing directory that you can configure by\
passing the `images_dir` argument. By default, the images are written to\
`dataset_dir`.\
\
Currently, only single frame images are supported, but a community\
contribution to support 3D or 4D image types (e.g., CT scans) is welcomed!\
\
Note\
\
See `DICOMDatasetImporter`\
for parameters that can be passed to methods like\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to\
customize the import of datasets of this type.\
\
You can create a FiftyOne dataset from a DICOM dataset stored in standard\
format as follows:\
\
You can create a FiftyOne dataset from a glob pattern of DICOM files or the\
path to a DICOMDIR file as follows:\
\
## GeoJSONDataset [¶](\#geojsondataset "Permalink to this headline")\
\
The [`fiftyone.types.GeoJSONDataset`](../../api/fiftyone.types.html#fiftyone.types.GeoJSONDataset "fiftyone.types.GeoJSONDataset") type represents a dataset consisting\
of images or videos and their associated geolocation data and optional\
properties stored in [GeoJSON format](https://en.wikipedia.org/wiki/GeoJSON).\
\
Datasets of this type are read in the following format:\
\
```\
<dataset_dir>/\
    data/\
        <filename1>.<ext>\
        <filename2>.<ext>\
        ...\
    labels.json\
\
```\
\
where `labels.json` is a GeoJSON file containing a `FeatureCollection` in the\
following format:\
\
```\
{\
    "type": "FeatureCollection",\
    "features": [\
        {\
            "type": "Feature",\
            "geometry": {\
                "type": "Point",\
                "coordinates": [\
                    -73.99496451958454,\
                    40.66338032487842\
                ]\
            },\
            "properties": {\
                "filename": <filename1>.<ext>,\
                ...\
            }\
        },\
        {\
            "type": "Feature",\
            "geometry": {\
                "type": "Point",\
                "coordinates": [\
                    -73.80992143421788,\
                    40.65611832778962\
                ]\
            },\
            "properties": {\
                "filename": <filename2>.<ext>,\
                ...\
            }\
        },\
        ...\
    ]\
}\
\
```\
\
where the `geometry` field may contain any valid GeoJSON geometry object, and\
the `filename` property encodes the name of the corresponding media in the\
`data/` folder. The `filename` property can also be an absolute path, which\
may or may not be in the `data/` folder.\
\
Samples with no location data will have a null `geometry` field.\
\
The `properties` field of each feature can contain additional labels that\
can be imported.\
\
Note\
\
See [`GeoJSONDatasetImporter`](../../api/fiftyone.utils.geojson.html#fiftyone.utils.geojson.GeoJSONDatasetImporter "fiftyone.utils.geojson.GeoJSONDatasetImporter")\
for parameters that can be passed to methods like\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to\
customize the import of datasets of this type.\
\
You can create a FiftyOne dataset from a GeoJSON dataset stored in the above\
format as follows:\
\
You can also independently specify the locations of the labels and the root\
directory containing the corresponding media files by providing the\
`labels_path` and `data_path` parameters rather than `dataset_dir`:\
\
Note\
\
If the `filename` key of your labels contains absolute paths to the source\
media, then you can omit the `data_path` parameter from the example above.\
\
## GeoTIFFDataset [¶](\#geotiffdataset "Permalink to this headline")\
\
The [`fiftyone.types.GeoTIFFDataset`](../../api/fiftyone.types.html#fiftyone.types.GeoTIFFDataset "fiftyone.types.GeoTIFFDataset") type represents a dataset consisting\
of images and their associated geolocation data stored in\
[GeoTIFF format](https://en.wikipedia.org/wiki/GeoTIFF).\
\
Note\
\
You must have [rasterio](https://github.com/mapbox/rasterio) installed in\
order to load GeoTIFF datasets.\
\
The standard format for datasets of this type is the following:\
\
```\
<dataset_dir>/\
    <filename1>.tif\
    <filename2>.tif\
\
```\
\
where each `.tif` file is a GeoTIFF image that can be read via\
[`rasterio.open`](https://rasterio.readthedocs.io/en/latest/api/rasterio.html#rasterio.open "(in rasterio)").\
\
Alternatively, rather than providing a `dataset_dir`, you can provide the\
`image_path` argument, which can directly specify a list or glob pattern of\
GeoTIFF images to load.\
\
The dataset will contain a [`GeoLocation`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.GeoLocation "fiftyone.core.labels.GeoLocation") field whose\
[`point`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.GeoLocation.point "fiftyone.core.labels.GeoLocation.point") attribute contains the\
`(longitude, latitude)` coordinates of each image center and whose\
[`polygon`](../../api/fiftyone.core.labels.html#fiftyone.core.labels.GeoLocation.polygon "fiftyone.core.labels.GeoLocation.polygon") attribute contains\
the `(longitude, latitude)` coordinates of the corners of the image (clockwise,\
starting from the top-left corner).\
\
Note\
\
See [`GeoTIFFDatasetImporter`](../../api/fiftyone.utils.geotiff.html#fiftyone.utils.geotiff.GeoTIFFDatasetImporter "fiftyone.utils.geotiff.GeoTIFFDatasetImporter")\
for parameters that can be passed to methods like\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to\
customize the import of datasets of this type.\
\
You can create a FiftyOne dataset from a GeoTIFF dataset stored in standard\
format as follows:\
\
You can create a FiftyOne dataset from a list or glob pattern of GeoTIFF images\
as follows:\
\
## FiftyOneDataset [¶](\#fiftyonedataset "Permalink to this headline")\
\
The [`fiftyone.types.FiftyOneDataset`](../../api/fiftyone.types.html#fiftyone.types.FiftyOneDataset "fiftyone.types.FiftyOneDataset") provides a disk representation of\
an entire [`Dataset`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset "fiftyone.core.dataset.Dataset") in a serialized JSON format along with its source media.\
\
Datasets of this type are read in the following format:\
\
```\
<dataset_dir>/\
    metadata.json\
    samples.json\
    data/\
        <filename1>.<ext>\
        <filename2>.<ext>\
        ...\
    annotations/\
        <anno_key1>.json\
        <anno_key2>.json\
        ...\
    brain/\
        <brain_key1>.json\
        <brain_key2>.json\
        ...\
    evaluations/\
        <eval_key1>.json\
        <eval_key2>.json\
        ...\
\
```\
\
where `metadata.json` is a JSON file containing metadata associated with the\
dataset, `samples.json` is a JSON file containing a serialized representation\
of the samples in the dataset, `annotations/` contains any serialized\
[`AnnotationResults`](../../api/fiftyone.core.annotation.html#fiftyone.core.annotation.AnnotationResults "fiftyone.core.annotation.AnnotationResults"), `brain/` contains any serialized [`BrainResults`](../../api/fiftyone.core.brain.html#fiftyone.core.brain.BrainResults "fiftyone.core.brain.BrainResults"), and\
`evaluations/` contains any serialized [`EvaluationResults`](../../api/fiftyone.core.evaluation.html#fiftyone.core.evaluation.EvaluationResults "fiftyone.core.evaluation.EvaluationResults").\
\
The contents of the `data/` directory may also be organized in nested\
subfolders, depending on how the dataset was exported, in which case the\
filepaths in `samples.json` should contain corerspondingly nested paths.\
\
Video datasets have an additional `frames.json` file that contains a serialized\
representation of the frame labels for each video in the dataset.\
\
Note\
\
See [`FiftyOneDatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.FiftyOneDatasetImporter "fiftyone.utils.data.importers.FiftyOneDatasetImporter")\
for parameters that can be passed to methods like\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") to\
customize the import of datasets of this type.\
\
You can create a FiftyOne dataset from a directory in the above format as\
follows:\
\
If you performed a [FiftyOneDataset export](../export_datasets.md#fiftyonedataset-export)\
using the `rel_dir` parameter to strip a common prefix from the media filepaths\
in the dataset, then simply include the `rel_dir` parameter when importing back\
into FiftyOne to prepend the appropriate prefix to each media path:\
\
Note\
\
Exporting in [FiftyOneDataset format](../export_datasets.md#fiftyonedataset-export) using\
the `export_media=False` and `rel_dir` parameters is a convenient way to\
transfer datasets between work environments, since this enables you to\
store the media files wherever you wish in each environment and then simply\
provide the appropriate `rel_dir` value as shown above when importing the\
dataset into FiftyOne in a new environment.\
\
## Custom formats [¶](\#custom-formats "Permalink to this headline")\
\
If your data does not follow one of the previous formats, then the simplest and\
most flexible approach to loading your data into FiftyOne is [to iterate over\\
your data in a Python loop](index.html#loading-custom-datasets) and add it to a [`Dataset`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset "fiftyone.core.dataset.Dataset").\
\
Alternatively, the [`Dataset`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset "fiftyone.core.dataset.Dataset") class provides a\
[`Dataset.from_importer()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_importer "fiftyone.core.dataset.Dataset.from_importer")\
factory method that can be used to import a dataset using any [`DatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter "fiftyone.utils.data.importers.DatasetImporter")\
instance.\
\
This means that you can define your own [`DatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter "fiftyone.utils.data.importers.DatasetImporter") class and then import\
a dataset from disk in your custom format using the following recipe:\
\
```\
import fiftyone as fo\
\
# Create an instance of your custom dataset importer\
importer = CustomDatasetImporter(...)\
\
# Import the dataset\
dataset = fo.Dataset.from_importer(importer)\
\
```\
\
You can also define a custom [`Dataset`](../../api/fiftyone.types.html#fiftyone.types.Dataset "fiftyone.types.Dataset") type, which enables you to import\
datasets in your custom format using the\
[`Dataset.from_dir()`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.from_dir "fiftyone.core.dataset.Dataset.from_dir") factory\
method:\
\
```\
import fiftyone as fo\
\
# The `fiftyone.types.Dataset` subclass for your custom dataset\
dataset_type = CustomDataset\
\
# Import the dataset\
dataset = fo.Dataset.from_dir(dataset_type=dataset_type, ...)\
\
```\
\
### Writing a custom DatasetImporter [¶](\#writing-a-custom-datasetimporter "Permalink to this headline")\
\
[`DatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter "fiftyone.utils.data.importers.DatasetImporter") is an abstract interface; the concrete interface that you\
should implement is determined by the type of dataset that you are importing.\
\
### Importing dataset-level information [¶](\#importing-dataset-level-information "Permalink to this headline")\
\
The\
[`has_dataset_info`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter.has_dataset_info "fiftyone.utils.data.importers.DatasetImporter.has_dataset_info")\
property of the importer allows it to declare whether its\
[`get_dataset_info()`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter.get_dataset_info "fiftyone.utils.data.importers.DatasetImporter.get_dataset_info")\
method should be called after all samples have been imported to retrieve a dict\
of dataset-level information to store in the\
[`info`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.info "fiftyone.core.dataset.Dataset.info") property of the dataset.\
\
As a special case, if the `info` dict contains any of the keys listed below,\
these items are popped and stored in the corresponding dedicated dataset field:\
\
- `"classes"` key:\
[`Dataset.classes`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.classes "fiftyone.core.dataset.Dataset.classes")\
\
- `"default_classes"` key:\
[`Dataset.default_classes`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.default_classes "fiftyone.core.dataset.Dataset.default_classes")\
\
- `"mask_targets"` key:\
[`Dataset.mask_targets`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.mask_targets "fiftyone.core.dataset.Dataset.mask_targets")\
\
- `"default_mask_targets"` key:\
[`Dataset.default_mask_targets`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.default_mask_targets "fiftyone.core.dataset.Dataset.default_mask_targets")\
\
- `"skeletons"` key:\
[`Dataset.skeletons`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.skeletons "fiftyone.core.dataset.Dataset.skeletons")\
\
- `"default_skeleton"` key:\
[`Dataset.default_skeleton`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.default_skeleton "fiftyone.core.dataset.Dataset.default_skeleton")\
\
- `"app_config"` key:\
[`Dataset.app_config`](../../api/fiftyone.core.dataset.html#fiftyone.core.dataset.Dataset.app_config "fiftyone.core.dataset.Dataset.app_config")\
\
\
### Writing a custom Dataset type [¶](\#writing-a-custom-dataset-type "Permalink to this headline")\
\
FiftyOne provides the [`Dataset`](../../api/fiftyone.types.html#fiftyone.types.Dataset "fiftyone.types.Dataset") type system so that dataset formats can be\
conveniently referenced by their type when reading/writing datasets on disk.\
\
The primary function of the [`Dataset`](../../api/fiftyone.types.html#fiftyone.types.Dataset "fiftyone.types.Dataset") subclasses is to define the\
[`DatasetImporter`](../../api/fiftyone.utils.data.importers.html#fiftyone.utils.data.importers.DatasetImporter "fiftyone.utils.data.importers.DatasetImporter") that should be used to read instances of the dataset from\
disk and the [`DatasetExporter`](../../api/fiftyone.utils.data.exporters.html#fiftyone.utils.data.exporters.DatasetExporter "fiftyone.utils.data.exporters.DatasetExporter") that should be used to write instances of the\
dataset to disk.\
\
See [this page](../export_datasets.md#writing-a-custom-dataset-exporter) for more information\
about defining custom [`DatasetExporter`](../../api/fiftyone.utils.data.exporters.html#fiftyone.utils.data.exporters.DatasetExporter "fiftyone.utils.data.exporters.DatasetExporter") classes.\
\
Custom dataset types can be declared by implementing the [`Dataset`](../../api/fiftyone.types.html#fiftyone.types.Dataset "fiftyone.types.Dataset") subclass\
corresponding to the type of dataset that you are working with.\
\
