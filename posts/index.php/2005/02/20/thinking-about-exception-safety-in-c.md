<html><body><p>Thanks to David Abrahams, we have a framework to discuss the relative <a href="http://www.boost.org/more/generic_exception_safety.html">exception safety</a> of C++ components. Quick reiteration:<br># The basic guarantee: that the invariants of the component are preserved, and no resources are leaked.<br># The strong guarantee: that the operation has either completed successfully or thrown an exception, leaving the program state exactly as it was before the operation started.<br># The no-throw guarantee: that the operation will not throw an exception.<br><br>A corner case, that came up recently in discussion about shared_ptr, is how to describe the situation where the invariants of the component are preserved, the state of the component is what it was before, but the state of the <span>program</span> is not exactly as it was before the operation started.<br><br>shared_ptr potentially can throw an exception during construction. If it does, the pointer that it is constructed on is deleted (or the deleter called). This is important, as it prevents any resource leak. But it does mean that there is a change in the state of the program. Whatever the pointer is pointing to is gone. Does this mean that shared_ptr only provides the basic guarantee?<br><br>That would be unsatsifying.<br><br>The only way out of it that I see is that any attempt to observe the changed state would involve undefined behavior. And it would be the same undefined behavior, at that point in the code, if the operation did succeed.<br><br>Concrete, simple, case:<br><br>Widget * w = new Widget; // 1<br>// ... some more code<br>shared_ptr sp_w = shared_ptr<widget>(w); // 2 <br>// ... yet more code.<br>w-&gt;widgetOp(); // 3<br><br><br>How could we arrive at 3 if an exception was thrown at 2? Only if 2 was inside a try block of some kind, which implies at least one block scope level difference between //1 and //2. <br><br>Which means that when that scope was exited, sp_w would have gone out of scope and been deleted, so that any reference to w at //3 would be invalid.<br><br>It seems to me that the program state in the strong guarantee must be one that is marked as being the beginning of a transaction by opening a try block. That this is necessary, although not sufficient for transactional behavior. Of course to accomplish this, each component must revert its state to the initial conditions, no component can know enough to revert the state of the entire program.</widget></p></body></html>