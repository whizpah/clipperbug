# Problem with Clipper and Intersect on certain polygons

I'm working on a project with a lot of polygons with different shapes and sizes. Clipper works for 99% of them, but 
sometimes it freezes in this portion of the code:

```go
func (c *Clipper) GetBottomPt(pp *OutPt) *OutPt {
	var dups *OutPt
	p := pp.Next
	for p != pp {
		if p.Pt.Y > pp.Pt.Y {
			pp = p
			dups = nil
		} else if p.Pt.Y == pp.Pt.Y && p.Pt.X <= pp.Pt.X {
			if p.Pt.X < pp.Pt.X {
				dups = nil
				pp = p
			} else {
				if p.Next != pp && p.Prev != pp {
					dups = p
				}
			}
		}
		p = p.Next
	}
	if dups != nil {
		//there appears to be at least 2 vertices at bottomPt so ...
		for dups != p {
			if !c.FirstIsBottomPt(p, dups) {
				pp = dups
			}
			dups = dups.Next
			for dups.Pt != pp.Pt {
				dups = dups.Next
			}
		}
	}
	return pp
}
```

Specifically in this loop:

```go
if dups != nil {
    //there appears to be at least 2 vertices at bottomPt so ...
    for dups != p {
        if !c.FirstIsBottomPt(p, dups) {
        pp = dups
        }
        dups = dups.Next
        for dups.Pt != pp.Pt {
            dups = dups.Next
        }
    }
}
```

The attached test `TestClipperIntersection`showcases just one of the many polygons that cause this issue.

To run the program (you might have to download gocv and clipper first):

`go test -v -run TestClipperIntersection`
