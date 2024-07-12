# Lazy-Propagation-in-Segment-Trees
This repository implements a segment tree data structure with lazy propagation. This technique efficiently handles range updates and queries, making it suitable for scenarios where frequent updates and queries over a range of elements are required.
class SegmentTree:
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (4 * n)
        self.lazy = [0] * (4 * n)

    def build(self, arr, start, end, node):
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            self.build(arr, start, mid, 2 * node + 1)
            self.build(arr, mid + 1, end, 2 * node + 2)
            self.tree[node] = self.tree[2 * node + 1] + self.tree[2 * node + 2]

    def update_range(self, start, end, l, r, val, node):
        if self.lazy[node] != 0:
            self.tree[node] += (end - start + 1) * self.lazy[node]
            if start != end:
                self.lazy[2 * node + 1] += self.lazy[node]
                self.lazy[2 * node + 2] += self.lazy[node]
            self.lazy[node] = 0

        if start > end or start > r or end < l:
            return

        if start >= l and end <= r:
            self.tree[node] += (end - start + 1) * val
            if start != end:
                self.lazy[2 * node + 1] += val
                self.lazy[2 * node + 2] += val
            return

        mid = (start + end) // 2
        self.update_range(start, mid, l, r, val, 2 * node + 1)
        self.update_range(mid + 1, end, l, r, val, 2 * node + 2)
        self.tree[node] = self.tree[2 * node + 1] + self.tree[2 * node + 2]

    def query_range(self, start, end, l, r, node):
        if self.lazy[node] != 0:
            self.tree[node] += (end - start + 1) * self.lazy[node]
            if start != end:
                self.lazy[2 * node + 1] += self.lazy[node]
                self.lazy[2 * node + 2] += self.lazy[node]
            self.lazy[node] = 0

        if start > end or start > r or end < l:
            return 0

        if start >= l and end <= r:
            return self.tree[node]

        mid = (start + end) // 2
        left = self.query_range(start, mid, l, r, 2 * node + 1)
        right = self.query_range(mid + 1, end, l, r, 2 * node + 2)
        return left + right

# Example usage
arr = [1, 3, 5, 7, 9, 11]
n = len(arr)
seg_tree = SegmentTree(n)
seg_tree.build(arr, 0, n - 1, 0)
seg_tree.update_range(0, n - 1, 1, 3, 10, 0)
print(seg_tree.query_range(0, n - 1, 1, 3, 0))
