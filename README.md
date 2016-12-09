# EasyDL

_EasyDL_ makes it easy to **download multiple files** in Swift.

```swift
let downloader = Downloader(items: [(url1, file1), (url2, file2)])

downloader.handleProgress { bytesDownloaded, bytesExpectedToDownload in
    print("\(bytesDownloaded) / \(bytesExpectedToDownload!)")
}

downloader.handleCompletion { result in
    switch result {
    case .success:
        let data1 = try! Data(contentsOf: URL(fileURLWithPath: file1))
        
        let data2 = try! Data(contentsOf: URL(fileURLWithPath: file2))
    case .canceled:
        ...
    case let .failure(error):
        ...
    }
}
```

## Flexible strategies

It is possible to choose download strategies for a `Downloader` and/or each `Item`.

```swift
enum Strategy {
    case always, ifUpdated, ifNotCached
}
```

```swift
let item1 = Item(url: url1, destination: file1) // `.ifUpdated` is a default value 
let item2 = Item(url: url2, destination: file2, strategy: .always)
let item3 = Item(url: url3, destination: file3, strategy: .ifNotCached)

let downloader = Downloader(items: [item1, item2, item3])
```

## Flexible progress handling

Following three overloads of `handleProgress` are available.

```swift
downloader.handleProgress { (bytesDownloaded: Int64, bytesExpectedToDownload: Int64?) in
    print("\(bytesDownloaded) / \(bytesExpectedToDownload!)")
}
```

```swift
downloader.handleProgress { (rate: Float?) in
    print("\(rate!) / 1.0")
}
```

```swift
downloader.handleProgress { (
    bytesDownloaded: Int64,
    bytesExpectedToDownload: Int64?,
    currentItemIndex: Int,
    bytesDownloadedForCurrentItem: Int64,
    bytesExpectedToDownloadForCurrentItem: Int64?
) in
    print("\(currentItemIndex) / \(downloader.items.count)")
}
```

Also precise progress or non-precise progress can be designated.

```swift
let downloader = Downloader(items: [(url1, file1), (url2, file2)], needsPreciseProgress: false)
```

Usually, a `Downloader` gets sizes of the `Item`s by sending HEAD requests and summing up `Content-Length`s in the response headers before starting downloads. When `needsPreciseProgress` is `false`, a `Downloader` omits those HEAD request. Then `handleProgress` for `Float?` calls a callback with pseudo progress, which is calculated on the assumption that all `Item`s has a same size. That is, the amout of the progress for one `Item` is `1.0 / Float(items.count)`.

## Installation

Use Swift Package Manager. Add the following to dependencies in your Package.swift file.

```swift
.Package(
    url: "https://github.com/koher/easy-dl.git",
    majorVersion: 0
)
```

## License

[The MIT License](LICENSE)