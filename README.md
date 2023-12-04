# FlickrMap
1.1 Classes Folder (only if you create the master-detail view from scratch)
Objective-C:
In the Classes folder, open the file MasterViewController.h, and change the
UITableViewController by UIViewController. The project should run again.
Remove or comment all the tablebiew related codes in the MasterViewController.m
Swift:
Open MasterViewController.swift, and change UITableViewController by UIViewController.
Remove or comment all the tableview related codes in the MasterViewController.swift
1.2 Add the map
We first declare the variable “MKMapView *map” to the controller “MasterViewController”:
Objective-c:


2 | P a g e
• In the MasterViewController.h file add “#import <MapKit/MapKit.h>” at the beginning
of the file,
• Add “@property (nonatomic, retain) IBOutlet MKMapView *map;” to the interface
• In the MasterViewController.m file, Add “@synthesize map” at the beginning of the
implementation.
Swift:
• Import MapKit in MasterViewController.swift before the class declaration
• Add “@IBOutlet weak var map: MKMapView!”
as a class var
Open the storyboard and click on the MasterViewController:
• Open the object library and add a Map View on the view frame of the Master View
Controller
• In the attribute inspector, associate it to the map Outlet to the MapView
• Add the appropriate constraints
• Save & Close.
1.3 Add the KML Parser [1][2][3]
Proceed, as follows.
Objective-c:
• The variable “KMLParser * kml” defined in “KMLParser.h” to the controller
“MasterViewController”
• The property and synthesis accordingly
• The following code in the function viewDidLoad of MasterViewController.m.
• In viewDidLoad remove also the edit and the plus button
NSURL *url = [NSURL URLWithString:
@"https://www.flickr.com/services/feeds/geo/fr?format=kml&page=1"];
self.kml = [KMLParser parseKMLAtURL:url];
NSArray *annotations = [kml points];
[map addAnnotations:annotations];
map.visibleMapRect = [kml pointsRect];
Swift:

3 | P a g e
override func viewDidLoad() {
super.viewDidLoad()
map.delegate = self
self.images = NSMutableArray()
let url = URL(string: "https://www.flickr.com/services/feeds/geo/fr?format=kml&page=1")
self.kml = KMLParser.parseKML(at: url)
if let annotationsArray = self.kml?.points as? [MKPointAnnotation] {
self.map.addAnnotations(annotationsArray)
self.map.showAnnotations(annotationsArray, animated: true)
self.map.visibleMapRect = self.kml.pointsRect()
}
navigationItem.leftBarButtonItem = editButtonItem
}
• Add kml variable, var kml: KMLParser! To the MasterViewController.swift
• Add the following code to the viewDidLoad
The map now knows about all the annotations we want to display, and you can already see
them in the map. Change the url for the country of your choice, for example:
• For Vietnam: https://www.flickr.com/services/feeds/geo/vn?format=kml&page=1
You can check the format of the file you receive from the URL.
Remember to insert these pieces of code in the viewDidLoad function!
Run and test. To zoom, use alt and click to zoom in and out.
1.4 Make the Pins in the Map nicer
We need to start by creating a delegate for the map: Open the storyboard, go to the connection
inspector and associate the delegate for the Map View Object by control dragging the outlet
delegate to the MKMapView. Save & Close. The delegate association makes that our object
MasterViewController now agrees to the MKMapViewDelegate protocol. Add protocol
MKMapViewDelegate to the MasterViewController.
Look at the documentation of MKMapViewDelegate to see what that protocol defines. Recall
from the lecture notes that:
• Protocols define a series of methods that classes want to conform to. A “protocol” is
defined like an “interface” without implementation.

4 | P a g e
Objective-C: We use < > brackets to give a comma separated list of protocols
Swif: we use comma separated format.
Now define one of the functions of the protocol at the end of the implementation of
MasterViewController, use for that the following codes:
Objective-C:
#pragma mark MKMapViewDelegate
- (MKAnnotationView *)mapView:(MKMapView *)mapView
viewForAnnotation:(id <MKAnnotation>)annotation {
NSLog(@”ViewForAnnotation”);
return [kml viewForAnnotation:annotation];
}
Swift:
func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) ->
MKAnnotationView? {
return kml.view(for: annotation)
}
1.5 Connect the detail view
Objective-C:
Add the variable “DetailViewController * detail” to the MasterViewController. Add property
and synthesis.
Swift:
NA.

5 | P a g e
1.6 Add button to the map pin
Replace the code of the function mapView of MasterViewController with that code
Objective-C:
MKAnnotationView * pin = [kml viewForAnnotation:annotation];
UIButton* rightButton = [UIButton buttonWithType:UIButtonTypeDetailDisclosure];
[rightButton addTarget:self action:@selector(showDetails:)
forControlEvents:UIControlEventTouchUpInside];
pin.rightCalloutAccessoryView = rightButton;
return pin;
Swift:
func mapView(_ mapView:MKMapView, viewFor annotation: MKAnnotation)-> MKAnnotationView?{
let pin: MKAnnotationView = kml.view(for: annotation)
let rightButton: UIButton! = UIButton(type: UIButton.ButtonType.detailDisclosure)
rightButton.addTarget(self,action:#selector(showDetails), for: UIControl.Event.touchUpInside)
rightButton.tag = images.count
if let url = kml.imageURL(for: annotation) {
self.images.add(url)
}
pin.rightCalloutAccessoryView = rightButton
return pin
}
In this new code, we use a @selector for a function called showDetails. Therefore, we need to
define also that function, use for that the following code
Objective-C:
Swift:
- (void)showDetails:(id)sender {}
func showDetail(sender: UIButton){}
* You will need to expose this function to Obj-c if asked for, as we are referring to it
1.7 Set the image to DetailViewController and show it
Add the following variables to the DetailViewController:
Objective-C:

6 | P a g e
• @property IBOutlet UIImageView * image;
• @property NSMutableArray * images;
• @property int currentIndex;
Swift:
• @IBOutlet weak var image: UIImageView!
• var images: NSMutableArray!
• var currentIndex : Int!
Open storyboard; change the attribute “Top Bar” to “None”. Now, remove the label, and add
a UIImageView object and associate to the outlet image in reference outlets.
Add the variable “NSMutableArray * images” to the controller MasterViewController.
Objective-C:
@property NSMutableArray * images in .h and synthesis in .m file.
Swift:
var images: NSMutableArray!
Add this code at the beginning of viewDidLoad:
Objective-C:
self.images = [NSMutableArray array];
Swift:
self.images = NSMutableArray()
Add the following code in the function mapView, just after the creation of rightButton:
Objective-C:
rightButton.tag = [images count];
NSURL * url = [kml imageURLForAnnotation:annotation];
[self.images addObject:url];
Swift:

7 | P a g e
Swift:
rightButton.tag = images.count
let url = kml.imageURL(for: annotation)
self.images.add(url)
Finally, add the following code to the function showDetails that we just defined previously.
Objective-C:
UIStoryboard *storyboard = self.storyboard;
detail = [storyboard
instantiateViewControllerWithIdentifier:@"DetailViewController"];
self.details.images = self.images;
UIButton * button = (UIButton *)sender;
[self.navigationController pushViewController:self.details animated:YES];
[self.details loadImage:button.tag];
Swift:
@objc func showDetails(sender: UIButton){
self.detailViewController = storyboard!.instantiateViewController(withIdentifier: "DetailViewController") as?
DetailViewController
let button = sender
self.detailViewController!.images = self.images
self.navigationController?.pushViewController(self.detailViewController!, animated: true)
self.detailViewController?.loadimage(index: button.tag)
}
In the identity inspector, set the storyboard ID of the DetailViewController (select the exact
controller) to “DetailViewController”, so as to get a reference to the detail view.
1.8 Load the image in the detailview
Add a new function to load the images in the detailViewController. Since the first generation of
iPhones (with only one CPU) and iOS 1, we can create multithreaded applications. Those were
mainly used to separate long processing times from UI updates, in a way that the user has

8 | P a g e
always visual feedback even though the application is calculating. In this application, when we
display the details of an image there is a couple of seconds where the interface seems locked
until the image is displayed. We are going to change that, use the following code to create a
thread:
Objective-C:
-(void)loadImage:(int)index{
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DE FAULT,0),
^{
//UIButton * button = (UIButton *)sender;
NSURL * url = (NSURL *)[images objectAtIndex:index]; NSData * data = [NSData
dataWithContentsOfURL:url]; UIImage* currentImage = [[UIImage alloc]initWithData:data];
dispatch_async(dispatch_get_main_queue(),^{
image.image = currentImage;
});
});
currentIndex = index;
}
Swift:
func loadimage(index: Int){
DispatchQueue.global(qos:.background).async{
let imageURL: NSURL! = self.images.object(at: index) as? NSURL
let data: NSData! = NSData(contentsOf: imageURL as URL)
print("Data size: %d", data!.length)
DispatchQueue.main.async{
self.image.image = UIImage(data: data as Data)
}
}
}
9 | P a g e
2 Exercises
1. The pointer to the image is null. Could you identify the problem? Other checkpoints:
o Make sure that you are delegating the MKMapViewDelegate
o Objective-C: Make sure that you synthesis map,kml,images,detail in the
MasterViewController and image in the DetailViewController
2. Look in the code you wrote where we can find the size of the image and resize the
UIImageView, used to display it, in a way that the aspect of the image does not get
changed.
o Tip: look at the “self.detail.image.contentMode”
3. Find on the help how to add an icon to the application and use the file icon.png for that
purpose.
4. Add also a UIActivityIndicatorView to inform the user that something is being processed.
5. Using a UIScrollView add multi-finger zoom to the image.
6. Using a UISwipeGestureRecognizer make the swipe gesture go from one picture to the
next
