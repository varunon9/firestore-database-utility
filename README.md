# firestore-database-utility
An utility class to access firestore database for Android (getOne, create, update, getMany).

[FirestoreDbUtility.java](https://github.com/varunon9/firestore-database-utility/blob/master/FirestoreDbUtility.java) is wrapper class to interact with firestore database in Android. Copy and paste this class along with all other helper classes and interface- [FirestoreQueryConditionCode.java](https://github.com/varunon9/firestore-database-utility/blob/master/FirestoreQueryConditionCode.java), [FirestoreQuery.java](https://github.com/varunon9/firestore-database-utility/blob/master/FirestoreQuery.java), [FirestoreDbOperationCallback.java](https://github.com/varunon9/firestore-database-utility/blob/master/FirestoreDbOperationCallback.java) in your android project.

## Sample Usage-

First of all create an instance of `FirestoreDbUtility` in your `ExampleActivity` something like-

`FirestoreDbUtility firestoreDbUtility = new FirestoreDbUtility();`

Define collection name-

`String collectionName = "users"; // collection (table) name `


### GET unique document from firestore collection

    ```
    String documentName = "4PkPVt4jGnO5neiKTKbe9I8B9yz2"; // may be uid of FirebaseUser i.e. firebaseUser.getUid()
    
    firestoreDbUtility.getOne(collectionName, documentName,
                new FirestoreDbOperationCallback() {
            @Override
            public void onSuccess(Object object) {
                DocumentSnapshot documentSnapshot = (DocumentSnapshot) object;
                User user = documentSnapshot.toObject(User.class); // User.java is a model class
                System.out.println(user.getEmail()); // do anything with your user object
            }

            @Override
            public void onFailure(Object object) {

            }
        });
    ```
    
### CREATE a document in firestore collection or MERGE if document already exists

    ```
    User user = new User(); // create an instance of User class and set relevant properties
    user.setEmail('email@example.com');
    user.setUid('4PkPVt4jGnO5neiKTKbe9I8B9yz2');
        
    firestoreDbUtility.createOrMerge(collectionName,
                  user.getUid(), user, new FirestoreDbOperationCallback() {
              @Override
              public void onSuccess(Object object) {
              }

              @Override
              public void onFailure(Object object) {
              }
          });
    ```
    
### UPDATE a document in firestore collection

    ```
    // set isOnline true of user
    Map<String, Object> hashMap = new HashMap<>();
    hashMap.put("online", true);
    
    firestoreDbUtility.update(collectionName,
                  firebaseUser.getUid(), hashMap, new FirestoreDbOperationCallback() {
              @Override
              public void onSuccess(Object object) {
              }

              @Override
              public void onFailure(Object object) {
              }
          });
    ```
    
### QUERY or getMany documents from firestore collection

    ```
    GeoPoint lesserGeoPoint = new GeoPoint(12.1, 77.1); // latitude is 12.1 and longitude is 77.1
    GeoPoint greaterGeoPoint = new GeoPoint(13.0, 78);
    
    List<FirestoreQuery> firestoreQueryList = new ArrayList<>();
    firestoreQueryList.add(new FirestoreQuery(
            FirestoreQueryConditionCode.WHERE_LESS_THAN,
            "location",
            greaterGeoPoint
    ));
    firestoreQueryList.add(new FirestoreQuery(
            FirestoreQueryConditionCode.WHERE_GREATER_THAN,
            "location",
            lesserGeoPoint
    ));
    
    firestoreDbUtility.getMany(collectionName,
                firestoreQueryList, new FirestoreDbOperationCallback() {
            @Override
            public void onSuccess(Object object) {
                QuerySnapshot querySnapshot = (QuerySnapshot) object;
                for (DocumentSnapshot documentSnapshot: querySnapshot.getDocuments()) {
                    User user = documentSnapshot.toObject(User.class); // do something with user object
                }
            }

            @Override
            public void onFailure(Object object) {
                showMessage("Failed to locate nearby travellers.");
            }
        });
    
    ```
