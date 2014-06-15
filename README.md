# BULLETPROOF
#### SECURE PHP IMAGE UPLOADER
This is a one-file alternative for uploading, cropping, resizing and watermarking
images in PHP, as opposed to using huge/verbose libraries. All done with the best security. 

##### **Enable** `php_exif` extension in your php.ini before using this class.
=====
##### First Step;
````php
/* As usual: Require and then call the class */
require_once  "lib\bulletproof.php";
$bulletProof = new ImageUploader\BulletProof;
````

##### SCENARIO 1: Uploading images with the default setting. (Less code)
````php
/* This will use the default settings of the class and will upload only
 * (jpg, gif, png, jpeg) images with sizes ranging from 0.1kb to max 30kbs
 * It will also create a folder called "uploads" with chmod 0666 if it does not exist.
 */ 
if($_FILES){
    echo $bulletProof->upload($_FILES['picture']);
}
````

##### SCENARIO 2: Upload images with different sizes/image types/dimensions (Moaarr code)
````php
/* fileTypes() - What type of images to upload. ex: jpg, gif, png..
 * uploadDir() - Create/Assign a folder name to store the uploads.
 * limitSize() - Set a limit on the min and max image size for uploads (in bytes)
 * limitDimension() - Set the max height and width of image upload  (in pixels)
 */
$bulletProof
    ->fileTypes(array("png", "jpeg"))
    ->uploadDir("my_pictures")
    ->limitSize(array("min"=>1000, "max"=>100000))
    ->limitDimension(array("height"=>100, "width"=>100));
    ->upload($_FILES['picture']);

/* Always, use try/catch to handle errors, if there are no errors the variable
 * $bulletProof will contain the uploadDir/image of the uploaded image.
 * So, you can simply store it in db or echo it like  <img src='$bulletProof' />;        
 */
````

##### SCENARIO 3: Shrink / Resize and upload image.
````php
/* shrink() - will shrink/resize the image according to the given dimensions (in pixels) */
$bulletProof
    ->fileTypes(array("jpg", "gif", "png", "jpeg"))
    ->uploadDir("shrinked_images")
    ->shrink(array("height"=>100, "width"=>200))
    ->upload($_FILES["pictures"]);
````

##### SCENARIO 4: Watermark and upload image.
````php
/* watermark() - will accept two arguments.
 * First: The image to use as watermark. (best to use a PNG).
 * Second: The Location where to put your watermark on the image.
 * Location can be a string 'center', 'bottom-right', 'bottom-left', 'top-left'...
 */
$bulletProof
    ->fileTypes(array("png"))
    ->uploadDir("watermarked")
    ->watermark('watermark.png', 'bottom-right'))
    ->upload($_FILES['logo']);
````

##### SCENARIO 5: Crop and upload image
````php
/* crop() - Width and height (in pixels) for image crop.
 * crop is not like shrink, it simply will trim/cut the image
 * and return what is left, whereas shrink will not trim the image.
 */
$bulletProof
    ->fileTypes(array("png"))
    ->uploadDir("watermarked")
    ->crop(array("height"=>40, "width"=>50))
    ->upload($_FILES['logo']);
````

Please check the examples.php for more functions and all tested examples.


#### NOTE:
 The `upload()` method accepts two arguments. First the Image, and second (optional), a new name for the image.
 If you provide a name, it will be used as a new name for the upload, if not a unique name will be generated.
````php
// Uploaded file will be renamed 'cheeez' plus the file mime type i.e (jpg/png/gif...).
->upload($_FILES['fileName'], 'cheeez');

// file will be named ex '1531e4b0e3bc82_QPIJLMPKQNJGF' plus the mime type
->upload($_FILES['fileName']);
````

The `change()` method is different from `upload()` and should not be mixed.
The `change()` will allow you to directly crop/resize/watermark an image that is already uploaded.
Think of it like this: you have a buch of images on your server, you would like to watermark, then 
you can simply do a loop over them using glob() or watever, and watermark/crop/shrink all or 
a specific one. 

```php
// The change method is like accessing any file physically and making change to it. 
//CROP IMAGES
$change = $bulletProof
 	->crop(array("height"=>10, "width"=>10))
 	->change("crop", "my_pictures/awesome.gif");

// WATERMARK IMAGES
$change = $bulletProof
 	->watermark("logo.png", "center")
 	->change("watermark", "my_pictures/passport.gif");

// SHRINK IMAGES
$change = $bulletProof
 	->shrink(array("height"=>30, "width"=>50))
 	->change("shrink", "my_pictures/paris.jpg");
````

### What makes this secure?
* It checks any errors thrown from the `$_FILES[]['error']` array. 
* It uses `exif_imagetype()` method to get the **real** `mime/image` type,
* Checks if image type exists in the expected types ie. `array('jpg', 'png', 'gif', 'jpeg')`
* Checks `getimagesize();` to see if the image has a valid width/height measurable in pixels.
* Uses `is_uploaded_file()` to check for a secure upload HTTP Post method, (extra security check).



#### TODO
* <del> Allow Image Resizing </del> Done.
* <del> Allow Image Watermarking </del> Done.
* <del> Allow Image Cropping </del> Done.
* <del> Handle Errors with Exceptions </del> Done.
* <del> Backward compatability for PHP 5.3 </del> Done. 
* Rebuild another class abiding to the [Single Responsibility Principle](http://en.wikipedia.org/wiki/Single_responsibility_principle)




###License  
[Luke 3:11](http://www.kingjamesbibleonline.org/Luke-3-11/) ( Free; No license! )
