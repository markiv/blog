---
layout: post
title:  "Dequeuing Cells the Easy Way"
date:   2019-04-10 16:22:49 +0200
categories: swift
---

How many times have you done something like this:

{% highlight swift %}
let cell = tableView.dequeueReusableCell(withIdentifier: "EventCell",
    for: indexPath) as! EventCell
{% endhighlight %}

The typo-prone string identifier, the forced cast... surely we can do better with Swift?

Let's add a couple of useful extensions to `UITableView` and its descendants:

{% highlight swift %}
extension NSObject {
    class var className: String {
        return NSStringFromClass(self).components(separatedBy: ".").last!
    }
}

extension UITableView {
    func dequeueCell<T>(for indexPath: IndexPath) -> T where T: UITableViewCell {
        return dequeueReusableCell(withIdentifier: T.className, for: indexPath) as! T
    }
    func dequeueCell<T>() -> T? where T: UITableViewCell {
        return dequeueReusableCell(withIdentifier: T.className) as? T
    }
}
{% endhighlight %}

This makes the assumption that the cell's reuse identifier is *identical* to its class name.

Now, thanks to Swift's type inference, all we need to do to get a cell of the correct type is:

{% highlight swift %}
let cell: EventCell = tableView.dequeueReusableCell(for: indexPath)
{% endhighlight %}