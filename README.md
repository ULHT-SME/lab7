# Lab7

## Description

This hands-on lab introduces students to Firebase Storage and device features by implementing profile picture upload functionality. Students will learn to access the device camera and gallery, upload images to Firebase Storage, and link image URLs with user profiles in Firestore. By the end of this lab, students will have built a complete profile picture management system with real-time updates.

## Learning Objectives

By completing this lab, students will be able to:

- Access device camera and photo gallery using the image_picker plugin
- Configure platform-specific permissions for Android and iOS media access
- Upload images to Firebase Storage with proper file naming and organization
- Retrieve download URLs from Firebase Storage after successful uploads
- Update Firestore documents to store image URLs linked to user profiles
- Display uploaded images using NetworkImage with proper error handling
- Implement loading states and user feedback during async upload operations
- Handle common errors in image selection and upload workflows

## Required Packages

Add the following packages to your project:

```bash
flutter pub add image_picker:^1.0.4
flutter pub add firebase_storage:^11.5.0
```

Verify your `pubspec.yaml` includes:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.0
  firebase_auth: ^4.15.0
  cloud_firestore: ^4.13.0
  firebase_storage: ^11.5.0
  image_picker: ^1.0.4
```

Run to install:

```bash
flutter pub get
```

## Platform Setup

### Android Configuration

1. **Add permissions to AndroidManifest.xml**

Open `android/app/src/main/AndroidManifest.xml` and add these permissions inside the `<manifest>` tag:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    
    <!-- Add these permissions -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
                     android:maxSdkVersion="32" />
    <uses-permission android:name="android.permission.CAMERA"/>
    
    <application
        ...>
        ...
    </application>
</manifest>
```

2. **Update minSdkVersion (if needed)**

In `android/app/build.gradle`, ensure:

```gradle
android {
    defaultConfig {
        minSdkVersion 21  // Minimum for image_picker
    }
}
```

### iOS Configuration

1. **Add permission descriptions to Info.plist**

Open `ios/Runner/Info.plist` and add these keys before the closing `</dict>` tag:

```xml
<dict>
    <!-- Existing keys... -->
    
    <!-- Add these new keys -->
    <key>NSPhotoLibraryUsageDescription</key>
    <string>We need access to your photo library to upload profile pictures.</string>
    
    <key>NSCameraUsageDescription</key>
    <string>We need access to your camera to take profile pictures.</string>
    
    <key>NSMicrophoneUsageDescription</key>
    <string>We need access to your microphone for video recording.</string>
</dict>
```

2. **Update iOS deployment target**

In `ios/Podfile`, ensure:

```ruby
platform :ios, '12.0'  # Minimum for image_picker
```

---

## Lab Tasks

### Task 1: Pick Image from Gallery 

**Objective:** Implement image selection from device gallery using image_picker.

**Your Challenge:**

Create a new service file `lib/services/image_service.dart`:

```dart
import 'dart:io';
import 'package:image_picker/image_picker.dart';

class ImageService {
  final ImagePicker _picker = ImagePicker();

  // Pick image from gallery
  Future<XFile?> pickImageFromGallery() async {
    try {
      // TODO: Use _picker.pickImage() to select from gallery
      // Hint: source: ImageSource.gallery
      
      return null; // Replace with actual XFile
    } catch (e) {
      print('Error picking image: $e');
      return null;
    }
  }

  // Pick image from camera
  Future<XFile?> pickImageFromCamera() async {
    try {
      // TODO: Use _picker.pickImage() to capture from camera
      // Hint: source: ImageSource.camera
      
      return null; // Replace with actual XFile
    } catch (e) {
      print('Error taking photo: $e');
      return null;
    }
  }
}
```

**Implementation Hints:**

For gallery selection:
```dart
final XFile? image = await _picker.pickImage(
  source: ImageSource.gallery,
  maxWidth: 1024,      // Limit image size
  maxHeight: 1024,
  imageQuality: 85,    // Compress image (0-100)
);
return image;
```

**Test Your Implementation:**

Create a simple test screen to verify image picking works:

```dart
// In your profile screen or settings screen
import 'dart:io';
import 'package:image_picker/image_picker.dart';

class TestImagePicker extends StatefulWidget {
  @override
  _TestImagePickerState createState() => _TestImagePickerState();
}

class _TestImagePickerState extends State<TestImagePicker> {
  XFile? _selectedImage;
  final ImageService _imageService = ImageService();

  Future<void> _pickImage() async {
    final image = await _imageService.pickImageFromGallery();
    if (image != null) {
      setState(() {
        _selectedImage = image;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        if (_selectedImage != null)
          Image.file(
            File(_selectedImage!.path),
            height: 200,
            width: 200,
            fit: BoxFit.cover,
          )
        else
          Container(
            height: 200,
            width: 200,
            color: Colors.grey[300],
            child: Icon(Icons.person, size: 100),
          ),
        SizedBox(height: 16),
        ElevatedButton(
          onPressed: _pickImage,
          child: Text('Pick Image'),
        ),
      ],
    );
  }
}
```

**Key Concepts:**
- `XFile` is the cross-platform file type returned by image_picker
- `ImageSource.gallery` for photo library access
- `ImageSource.camera` for camera capture
- Always check if the returned image is null (user cancelled)

### Task 2: Upload Image to Firebase Storage 

**Objective:** Upload selected images to Firebase Storage and retrieve download URLs.

**Your Challenge:**

Create `lib/services/storage_service.dart`:

```dart
import 'dart:io';
import 'package:firebase_storage/firebase_storage.dart';
import 'package:firebase_auth/firebase_auth.dart';

class StorageService {
  final FirebaseStorage _storage = FirebaseStorage.instance;
  final FirebaseAuth _auth = FirebaseAuth.instance;

  // Upload profile picture and return download URL
  Future<String?> uploadProfilePicture(String imagePath) async {
    try {
      // Get current user ID
      final userId = _auth.currentUser?.uid;
      if (userId == null) {
        throw Exception('No user logged in');
      }

      // TODO: Create a reference to storage location
      // Hint: Use path like 'profile_images/$userId.jpg'
      
      // TODO: Create File object from imagePath
      
      // TODO: Upload file using putFile()
      
      // TODO: Get download URL using getDownloadURL()
      
      return null; // Replace with actual download URL
    } catch (e) {
      print('Error uploading image: $e');
      return null;
    }
  }

  // Delete profile picture
  Future<bool> deleteProfilePicture() async {
    try {
      final userId = _auth.currentUser?.uid;
      if (userId == null) return false;

      // TODO: Create reference to user's profile image
      
      // TODO: Delete the file using delete()
      
      return true;
    } catch (e) {
      print('Error deleting image: $e');
      return false;
    }
  }
}
```

**Implementation Hints:**

For uploading:
```dart
// Get current user
final userId = _auth.currentUser?.uid;
if (userId == null) throw Exception('No user logged in');

// Create storage reference
final storageRef = _storage.ref().child('profile_images/$userId.jpg');

// Create file from path
final file = File(imagePath);

// Upload file
final uploadTask = await storageRef.putFile(file);

// Get download URL
final downloadUrl = await uploadTask.ref.getDownloadURL();

return downloadUrl;
```

**Understanding Firebase Storage Paths:**
```
Firebase Storage Root
└── profile_images/
    ├── user1_uid.jpg
    ├── user2_uid.jpg
    └── user3_uid.jpg
```

**Add Progress Tracking (Optional but Recommended):**

```dart
Future<String?> uploadProfilePictureWithProgress(
  String imagePath,
  Function(double) onProgress,
) async {
  try {
    final userId = _auth.currentUser?.uid;
    if (userId == null) throw Exception('No user logged in');

    final storageRef = _storage.ref().child('profile_images/$userId.jpg');
    final file = File(imagePath);

    // Create upload task
    final uploadTask = storageRef.putFile(file);

    // Listen to upload progress
    uploadTask.snapshotEvents.listen((TaskSnapshot snapshot) {
      double progress = snapshot.bytesTransferred / snapshot.totalBytes;
      onProgress(progress);
    });

    // Wait for upload to complete
    final snapshot = await uploadTask;
    final downloadUrl = await snapshot.ref.getDownloadURL();

    return downloadUrl;
  } catch (e) {
    print('Error uploading: $e');
    return null;
  }
}
```

**Test Upload:**

```dart
// In your test screen
Future<void> _uploadImage() async {
  if (_selectedImage == null) return;

  setState(() => _isUploading = true);

  final storageService = StorageService();
  final url = await storageService.uploadProfilePicture(_selectedImage!.path);

  setState(() => _isUploading = false);

  if (url != null) {
    print('Upload successful! URL: $url');
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('Image uploaded successfully!')),
    );
  } else {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text('Upload failed')),
    );
  }
}
```

### Task 3: Store Image URL in Firestore 

**Objective:** Link uploaded image URLs with user profiles in Firestore.

**Your Challenge:**

Update or create `lib/services/user_service.dart`:

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_auth/firebase_auth.dart';

class UserService {
  final FirebaseFirestore _firestore = FirebaseFirestore.instance;
  final FirebaseAuth _auth = FirebaseAuth.instance;

  // Update user profile picture URL
  Future<bool> updateProfilePicture(String imageUrl) async {
    try {
      final userId = _auth.currentUser?.uid;
      if (userId == null) return false;

      // TODO: Update the 'users' collection document with new photoURL
      // Hint: Use _firestore.collection('users').doc(userId).update()
      
      return true;
    } catch (e) {
      print('Error updating profile: $e');
      return false;
    }
  }

  // Get user profile data
  Stream<DocumentSnapshot> getUserProfile() {
    final userId = _auth.currentUser?.uid;
    if (userId == null) {
      throw Exception('No user logged in');
    }

    return _firestore.collection('users').doc(userId).snapshots();
  }

  // Create initial user profile (call this after registration)
  Future<void> createUserProfile(User user) async {
    try {
      await _firestore.collection('users').doc(user.uid).set({
        'email': user.email,
        'displayName': user.displayName ?? '',
        'photoURL': user.photoURL ?? '',
        'createdAt': FieldValue.serverTimestamp(),
      });
    } catch (e) {
      print('Error creating profile: $e');
    }
  }
}
```

**Implementation Hints:**

For updating profile picture:
```dart
final userId = _auth.currentUser?.uid;
if (userId == null) return false;

await _firestore.collection('users').doc(userId).update({
  'photoURL': imageUrl,
  'updatedAt': FieldValue.serverTimestamp(),
});

return true;
```

**Firestore Document Structure:**
```
users/
├── user1_uid
│   ├── email: "user1@example.com"
│   ├── displayName: "John Doe"
│   ├── photoURL: "https://firebasestorage.../profile.jpg"
│   ├── createdAt: Timestamp
│   └── updatedAt: Timestamp
└── user2_uid
    └── ...
```

### Task 4: Complete Upload Workflow 

**Objective:** Integrate image picking, uploading, and Firestore updates into one complete flow.

**Your Challenge:**

Create `lib/services/profile_service.dart` to orchestrate the complete workflow:

```dart
import 'package:image_picker/image_picker.dart';
import 'image_service.dart';
import 'storage_service.dart';
import 'user_service.dart';

class ProfileService {
  final ImageService _imageService = ImageService();
  final StorageService _storageService = StorageService();
  final UserService _userService = UserService();

  // Complete workflow: pick → upload → update Firestore
  Future<bool> updateProfilePicture({
    required ImageSource source,
    Function(double)? onProgress,
  }) async {
    try {
      // Step 1: Pick image
      XFile? image;
      if (source == ImageSource.gallery) {
        image = await _imageService.pickImageFromGallery();
      } else {
        image = await _imageService.pickImageFromCamera();
      }

      if (image == null) {
        print('No image selected');
        return false;
      }

      // Step 2: Upload to Firebase Storage
      final downloadUrl = await _storageService.uploadProfilePicture(image.path);
      
      if (downloadUrl == null) {
        print('Upload failed');
        return false;
      }

      // Step 3: Update Firestore
      final success = await _userService.updateProfilePicture(downloadUrl);
      
      return success;
    } catch (e) {
      print('Error in profile update workflow: $e');
      return false;
    }
  }

  // Delete profile picture workflow
  Future<bool> deleteProfilePicture() async {
    try {
      // Delete from Storage
      await _storageService.deleteProfilePicture();
      
      // Update Firestore (set to empty string)
      await _userService.updateProfilePicture('');
      
      return true;
    } catch (e) {
      print('Error deleting profile picture: $e');
      return false;
    }
  }
}
```

**Build Profile Picture Widget:**

Create `lib/widgets/profile_picture_widget.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:image_picker/image_picker.dart';
import '../services/profile_service.dart';
import '../services/user_service.dart';

class ProfilePictureWidget extends StatefulWidget {
  final double radius;
  
  ProfilePictureWidget({this.radius = 60});

  @override
  _ProfilePictureWidgetState createState() => _ProfilePictureWidgetState();
}

class _ProfilePictureWidgetState extends State<ProfilePictureWidget> {
  final ProfileService _profileService = ProfileService();
  final UserService _userService = UserService();
  bool _isUploading = false;

  Future<void> _showImageSourceDialog() async {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('Choose Image Source'),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            ListTile(
              leading: Icon(Icons.photo_library),
              title: Text('Gallery'),
              onTap: () {
                Navigator.pop(context);
                _updateProfilePicture(ImageSource.gallery);
              },
            ),
            ListTile(
              leading: Icon(Icons.camera_alt),
              title: Text('Camera'),
              onTap: () {
                Navigator.pop(context);
                _updateProfilePicture(ImageSource.camera);
              },
            ),
          ],
        ),
      ),
    );
  }

  Future<void> _updateProfilePicture(ImageSource source) async {
    setState(() => _isUploading = true);

    try {
      final success = await _profileService.updateProfilePicture(
        source: source,
      );

      if (success) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text('Profile picture updated!')),
        );
      } else {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text('Failed to update picture')),
        );
      }
    } catch (e) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Error: $e')),
      );
    } finally {
      setState(() => _isUploading = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return StreamBuilder<DocumentSnapshot>(
      stream: _userService.getUserProfile(),
      builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.waiting) {
          return CircleAvatar(
            radius: widget.radius,
            child: CircularProgressIndicator(),
          );
        }

        String? photoURL;
        if (snapshot.hasData && snapshot.data != null) {
          final data = snapshot.data!.data() as Map<String, dynamic>?;
          photoURL = data?['photoURL'] as String?;
        }

        return Stack(
          children: [
            // Profile Picture
            CircleAvatar(
              radius: widget.radius,
              backgroundColor: Colors.grey[300],
              backgroundImage: photoURL != null && photoURL.isNotEmpty
                  ? NetworkImage(photoURL)
                  : null,
              child: photoURL == null || photoURL.isEmpty
                  ? Icon(Icons.person, size: widget.radius, color: Colors.grey[600])
                  : null,
            ),

            // Loading Overlay
            if (_isUploading)
              Positioned.fill(
                child: Container(
                  decoration: BoxDecoration(
                    shape: BoxShape.circle,
                    color: Colors.black54,
                  ),
                  child: Center(
                    child: CircularProgressIndicator(color: Colors.white),
                  ),
                ),
              ),

            // Edit Button
            Positioned(
              bottom: 0,
              right: 0,
              child: GestureDetector(
                onTap: _isUploading ? null : _showImageSourceDialog,
                child: Container(
                  padding: EdgeInsets.all(8),
                  decoration: BoxDecoration(
                    color: Theme.of(context).primaryColor,
                    shape: BoxShape.circle,
                    border: Border.all(color: Colors.white, width: 2),
                  ),
                  child: Icon(
                    Icons.camera_alt,
                    size: 20,
                    color: Colors.white,
                  ),
                ),
              ),
            ),
          ],
        );
      },
    );
  }
}
```

### Task 5: Display Profile Picture with Real-Time Updates 

**Objective:** Show profile picture with StreamBuilder for automatic updates.

**Your Challenge:**

Integrate the ProfilePictureWidget into your profile or settings screen:

```dart
import 'package:flutter/material.dart';
import '../widgets/profile_picture_widget.dart';
import '../services/profile_service.dart';

class ProfileScreen extends StatelessWidget {
  final ProfileService _profileService = ProfileService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Profile'),
      ),
      body: Center(
        child: Padding(
          padding: EdgeInsets.all(24),
          child: Column(
            children: [
              SizedBox(height: 40),
              
              // Profile Picture Widget
              ProfilePictureWidget(radius: 80),
              
              SizedBox(height: 24),
              
              Text(
                'Tap camera icon to change picture',
                style: TextStyle(color: Colors.grey),
              ),
              
              SizedBox(height: 40),
              
              // Additional profile info
              Card(
                child: Padding(
                  padding: EdgeInsets.all(16),
                  child: Column(
                    children: [
                      ListTile(
                        leading: Icon(Icons.email),
                        title: Text('Email'),
                        subtitle: Text('user@example.com'),
                      ),
                      Divider(),
                      ListTile(
                        leading: Icon(Icons.person),
                        title: Text('Display Name'),
                        subtitle: Text('John Doe'),
                      ),
                    ],
                  ),
                ),
              ),
              
              SizedBox(height: 24),
              
              // Delete picture button
              OutlinedButton.icon(
                onPressed: () async {
                  final confirmed = await showDialog<bool>(
                    context: context,
                    builder: (context) => AlertDialog(
                      title: Text('Delete Profile Picture?'),
                      content: Text('This action cannot be undone.'),
                      actions: [
                        TextButton(
                          onPressed: () => Navigator.pop(context, false),
                          child: Text('Cancel'),
                        ),
                        ElevatedButton(
                          onPressed: () => Navigator.pop(context, true),
                          style: ElevatedButton.styleFrom(
                            backgroundColor: Colors.red,
                          ),
                          child: Text('Delete'),
                        ),
                      ],
                    ),
                  );

                  if (confirmed == true) {
                    await _profileService.deleteProfilePicture();
                    ScaffoldMessenger.of(context).showSnackBar(
                      SnackBar(content: Text('Profile picture removed')),
                    );
                  }
                },
                icon: Icon(Icons.delete, color: Colors.red),
                label: Text('Remove Picture', style: TextStyle(color: Colors.red)),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

## Optional Challenges

If you complete the main tasks early, try these enhancements:

### Challenge 1: Image Compression
Add image compression before upload to reduce file sizes:

```dart
// Add image package to pubspec.yaml
flutter pub add image:^4.1.3

// Compress image before upload
import 'package:image/image.dart' as img;

Future<File> compressImage(File file) async {
  final bytes = await file.readAsBytes();
  final image = img.decodeImage(bytes);
  
  if (image == null) return file;
  
  final compressed = img.encodeJpg(image, quality: 85);
  
  final compressedFile = File('${file.path}_compressed.jpg');
  await compressedFile.writeAsBytes(compressed);
  
  return compressedFile;
}
```

### Challenge 2: Multiple Image Formats
Support different image formats and naming:

```dart
Future<String?> uploadWithTimestamp(String imagePath) async {
  final userId = _auth.currentUser?.uid;
  final timestamp = DateTime.now().millisecondsSinceEpoch;
  final extension = imagePath.split('.').last;
  
  final storageRef = _storage.ref()
      .child('profile_images/$userId\_$timestamp.$extension');
  
  // Upload and return URL
}
```

### Challenge 3: Image Metadata
Store additional image metadata in Firestore:

```dart
await _firestore.collection('users').doc(userId).update({
  'photoURL': downloadUrl,
  'photoMetadata': {
    'uploadedAt': FieldValue.serverTimestamp(),
    'fileSize': file.lengthSync(),
    'fileName': file.path.split('/').last,
  },
});
```

### Challenge 4: Progress Bar
Add a visual progress bar for uploads:

```dart
class UploadProgressBar extends StatefulWidget {
  final Function(double) onUpload;
  
  @override
  _UploadProgressBarState createState() => _UploadProgressBarState();
}

class _UploadProgressBarState extends State<UploadProgressBar> {
  double _progress = 0.0;
  
  @override
  Widget build(BuildContext context) {
    return LinearProgressIndicator(
      value: _progress,
      backgroundColor: Colors.grey[300],
      valueColor: AlwaysStoppedAnimation<Color>(Colors.blue),
    );
  }
}
```

### Challenge 5: Image Cropping
Allow users to crop images before upload:

```dart
flutter pub add image_cropper:^5.0.0

// Implement cropping
Future<File?> cropImage(File imageFile) async {
  CroppedFile? croppedFile = await ImageCropper().cropImage(
    sourcePath: imageFile.path,
    aspectRatio: CropAspectRatio(ratioX: 1, ratioY: 1),
    uiSettings: [
      AndroidUiSettings(
        toolbarTitle: 'Crop Image',
        toolbarColor: Colors.blue,
        toolbarWidgetColor: Colors.white,
      ),
    ],
  );
  
  return croppedFile != null ? File(croppedFile.path) : null;
}
```

---


## Tips & Best Practices

### Common Issues and Solutions

**"Permission denied" errors on Android:**
- Check that all permissions are in AndroidManifest.xml
- For Android 13+, you may need to request permissions at runtime
- Test on device (permissions work differently on emulators)

**"No image selected" (image is null):**
- User cancelled the picker - this is normal behavior
- Always check if image is null before proceeding

**Upload fails silently:**
- Check Firebase Console → Storage for error logs
- Ensure Firebase Storage is enabled in Console
- Verify Storage rules allow writes (test mode for development)

**Image doesn't display after upload:**
- Verify download URL is correctly saved to Firestore
- Check network connectivity
- Ensure StreamBuilder is properly set up
- Look for CORS issues (mainly in web builds)

### Performance Tips

- **Compress images** before upload (85% quality is usually good)
- **Limit image dimensions** (1024x1024 is sufficient for profile pictures)
- **Use caching** for NetworkImage (Flutter does this automatically)
- **Show thumbnails** for image lists instead of full resolution

### Security Best Practices

**Firebase Storage Rules (add in Firebase Console → Storage → Rules):**

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // Allow users to upload their own profile pictures
    match /profile_images/{userId} {
      allow read: if true; // Anyone can view
      allow write: if request.auth != null && request.auth.uid == userId;
      allow delete: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

## Official Documentation Links

### Flutter Packages
- [image_picker documentation](https://pub.dev/packages/image_picker)
- [firebase_storage documentation](https://pub.dev/packages/firebase_storage)

### Firebase Documentation
- [Firebase Storage Get Started](https://firebase.google.com/docs/storage/flutter/start)
- [Upload Files with Flutter](https://firebase.google.com/docs/storage/flutter/upload-files)
- [Download Files with Flutter](https://firebase.google.com/docs/storage/flutter/download-files)
- [Storage Security Rules](https://firebase.google.com/docs/storage/security)

### Flutter Official Guides
- [Working with Images in Flutter](https://docs.flutter.dev/development/ui/assets-and-images)
- [Async Programming in Dart](https://dart.dev/codelabs/async-await)
- [Handling Errors in Flutter](https://docs.flutter.dev/cookbook/images/network-image)


## Deliverables

You are required to .zip file this project and deliver it in Moodle before the next class. The .zip should contain all the content of inside project folder with the exception of the .dart_tool and build folders (these will make the .zip file extremely large and they are not necessary for evaluation).
