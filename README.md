# firebase_cloud_issue

Example project to help call out a build problem for iOS and the [cloud_firestore: ^3.1.6](https://pub.dev/packages/cloud_firestore) package.

The issue is that adding this line to the Podfile to speed up the iOS build also breaks the builds:
```
  pod 'FirebaseFirestore', :git => 'https://github.com/invertase/firestore-ios-sdk-frameworks.git', :tag => '8.10.0'
```

When that is enabled and you run:
```
flutter clean && flutter packages get && cd ios && pod install --repo-update && cd .. && flutter build ios --release --no-codesign
```

... then the following error occurrs:
```
Failed to build iOS app
Error output from Xcode build:
↳
    ** BUILD FAILED **


Xcode's output:
↳
    Undefined symbols for architecture arm64:
      "leveldb::WriteBatch::WriteBatch()", referenced from:
          firebase::firestore::local::LevelDbTransaction::Commit() in
          FirebaseFirestore(leveldb_transaction.o)
      "leveldb::WriteBatch::Put(leveldb::Slice const&, leveldb::Slice const&)", referenced
      from:
          firebase::firestore::local::LevelDbTransaction::Commit() in
          FirebaseFirestore(leveldb_transaction.o)
      "leveldb::WriteBatch::~WriteBatch()", referenced from:
          firebase::firestore::local::LevelDbTransaction::Commit() in
          FirebaseFirestore(leveldb_transaction.o)
      "leveldb::Status::Status(leveldb::Status::Code, leveldb::Slice const&,
      leveldb::Slice const&)", referenced from:
          firebase::firestore::local::LevelDbTransaction::Get(absl::lts_2020_02_25::string
          _view, std::__1::basic_string<char, std::__1::char_traits<char>,
          std::__1::allocator<char> >*) in FirebaseFirestore(leveldb_transaction.o)
      "leveldb::Options::Options()", referenced from:
          firebase::firestore::local::LevelDbPersistence::OpenDb(firebase::firestore::util
          ::Path const&) in FirebaseFirestore(leveldb_persistence.o)
      "leveldb::WriteBatch::Delete(leveldb::Slice const&)", referenced from:
          firebase::firestore::local::LevelDbTransaction::Commit() in
          FirebaseFirestore(leveldb_transaction.o)
      "leveldb::Status::ToString() const", referenced from:
          firebase::firestore::local::ConvertStatus(leveldb::Status const&) in
          FirebaseFirestore(leveldb_util.o)
          firebase::firestore::local::LevelDbMigrations::ReadSchemaVersion(leveldb::DB*)
          in FirebaseFirestore(leveldb_migrations.o)
          firebase::firestore::local::LevelDbTransaction::Iterator::Seek(std::__1::basic_s
          tring<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in
          FirebaseFirestore(leveldb_transaction.o)
          firebase::firestore::local::LevelDbTransaction::Iterator::AdvanceLDB() in
          FirebaseFirestore(leveldb_transaction.o)
          firebase::firestore::local::LevelDbTransaction::Commit() in
          FirebaseFirestore(leveldb_transaction.o)
          firebase::firestore::local::LevelDbMutationQueue::LookupMutationBatch(int) in
          FirebaseFirestore(leveldb_mutation_queue.o)
          firebase::firestore::local::LevelDbRemoteDocumentCache::Get(firebase::firestore:
          :model::DocumentKey const&) in
          FirebaseFirestore(leveldb_remote_document_cache.o)
          ...
      "leveldb::DB::Open(leveldb::Options const&, std::__1::basic_string<char,
      std::__1::char_traits<char>, std::__1::allocator<char> > const&, leveldb::DB**)",
      referenced from:
          firebase::firestore::local::LevelDbPersistence::OpenDb(firebase::firestore::util
          ::Path const&) in FirebaseFirestore(leveldb_persistence.o)
    ld: symbol(s) not found for architecture arm64
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
    note: Using new build system
    note: Planning
    note: Build preparation complete
    note: Building targets in dependency order

```

## Workaround

Add `firebase_database: ^9.0.5` as dependency to the `pubspec.yaml` and rerun the above command to build the ios app and it it works.  On the plus side, the build time is massively reduced because the optimization can now be in place.  The down side is you may be bringing in a dependency you don't need to make that happen.